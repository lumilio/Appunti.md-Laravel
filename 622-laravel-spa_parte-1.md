
# Laravel SPA

Requirements:

- api endpoints (list of resources and single resource)
- Authentication with Vue scaffolding
  
## Install Vue Router

```bash
npm i vue-router
```

## Setup vue router

Follow the documentation steps

- Setup after npm installation <https://router.vuejs.org/installation.html#direct-download-cdn>
- Guide <https://router.vuejs.org/guide>

Change the app.js file and add the vue-router configuration

```js
// 0 Import Vue Router and use it
import Vue from 'vue';
import VueRouter from 'vue-router';
Vue.use(VueRouter);

//  1. Define few route pages components to start.
const Home = Vue.component('Home', require('./pages/Home.vue').default);
const About = Vue.component('About', require('./pages/About.vue').default);
const Contacts = Vue.component('Contacts', require('./pages/Contacts.vue').default);
// 2. Define some named routes and use the components defined above
const routes = [
    {
        path: '/',
        name: 'home',
        component: Home
    },
    {
        path: '/about',
        name: 'about',
        component: About
    },
    {
        path: '/contacts',
        name: 'contacts',
        component: Contacts
    },
]
// 3 Create e router instance
const router = new VueRouter({
    mode: 'history', // <-- HTML history mode to remove the # from the url
    routes
})

// 4 Inject the router instance
const app = new Vue({
    el: '#app',
    router
});
```

## Define the HTML for a vue router application

Following the guide <https://router.vuejs.org/guide/#html> build a layout file that can be extended by the welcome view and then create a main application component to use in the main applicaiton view (welcome.blade.php)

Copy the app.blade.php layout file and name it spa.blade.php then remove the navigation menu links and replace them with the vue-router `router-link` components

```php
cd layouts/
cp app.blade.php spa.blade.php

```

Update the nav links
then extract the navbar in a partial `partials/nav.blade.php`.

```php
  <router-link to="/" class="nav-link">Home</router-link>
  <router-link to="/about" class="nav-link">About</router-link>
  <router-link to="/contacts" class="nav-link">Contacts</router-link>
```

Put a @yield after the navbar, then define the main Application component

The final layout body will be:

```php
<body>
    <div id="app">
        @include('partials.nav')
        @yield('app')
    </div>
</body>
```

Let's define the main application component where we will use the `router-view` component to render the pages components.

```bash
cd resources/js
touch App.vue
```

Add this inside the app.js file

```js
Vue.component('App', require('./App.vue').default);

```

Create the application component that you can use as an entry point for all pages components

inside resources/js/pages

```vue
<template>
    <main class="py-4">
        <!-- This is the key as it let us render all pages contents -->
        <router-view></router-view> 
    </main>
</template>

<script>
export default {

}
</script>

<style>
</style>
```

Create the Pages Components in a Pages directory

```bash
cd resources/js/
mkdir pages
cd pages
touch About.vue
touch Home.vue
touch Contacts.vue

```

Define the pages vue structure for each page.

```vue
<template>
  <h1>HomePage</h1>
</template>

<script>
export default {

}
</script>

<style>

</style>
```

Let's first use the `/` route in web.php to see if out application works

```php
Route::get('/', function () {
    return view('guest.welcome');
});
```

Finally extend the spa layout in the welcome view and use the App component.

```php
@extends('layouts.spa')

@section('app')
    <App></App>
@endsection
```

Now if we browse our application we should be able to see out pages when we click on the nav menu items. However, if we click on the about page for example then we try to refresh the page we get a 404. That is because in laravel we have no routes defined for the about and contacts pages. The plan is to use only vue-router for these pages but still leave the admin and registration pages available as a non spa pages.

To fix this issue we can move the route `/` to the bottom of the file, and then create a catch all route. This will catch any route not defined above and let our vue application take care of it.

```php
Route::get('/{any}', function () {
    return view('guest.welcome');
})->where('any', '.*');
```

At this point we should be able to visit all pages (about, home, contacts), refresh and be still in that page.

Now we need to take care of the other pages.

Back to the app.js file lets define more routes' components pages and routes.

First we will add two pages that we will use to take care our games and single game views.

Lets start with a games.vue page

```js
const Games = Vue.component('Games', require('./pages/Games.vue').default);
```

Next define its route.

```js
    {
        path: '/games',
        name: 'games',
        component: Games
    }
```

Now create the Games.vue page in js/pages, here we use a `games-list` component
that we will build later

```vue
<template>
    <div>
        <h1>Games</h1>
        <games-list></games-list>
    </div>
</template>

<script>
import GamesListComponentVue from "../components/GamesListComponent.vue"
export default {
components: {GamesListComponentVue}

}
</script>

<style>
</style>
```

In the GamesList component we will make an axios call to fetch games from an api endpoint defined in api.php `Route::get('games', 'API\GameController@index');`

this is the index method

```php
  public function index()
    {
        //return Game::with(['category'])->paginate(5);
        return GameResource::collection(Game::with(['category'])->paginate(5));
    }
```

And this is our component, where we used `router-link` to generare a link to the single game view that we will build next.

```vue
<template>
    <section class="games d-flex flex-wrap">
        <div class="game" v-for="game in games">
            <div class="card">
                <img :src="'/storage/' + game.thumbnail" alt />
                <div class="card-body">
                    <h3>{{ game.name }}</h3>

                    <router-link :to="'/games/' + game.id">View Game</router-link>
                </div>
            </div>
        </div>
    </section>
</template>

<script>

export default {
    data() {
        return {
            loading: true,
            games: null,
            meta: null,
            links: null
        };
    },
    mounted() {
        axios.get("api/games").then((response) => {
            console.log(response);
            this.games = response.data.data;
            this.meta = response.data.meta;
            this.links = response.data.links;
            this.loading = false;
        });
        console.log("Component mounted.");
    },
};
</script>

```

Now lets build the single game page component and route.
To do that we need to use dynamic routes <https://router.vuejs.org/guide/essentials/dynamic-matching.html#reacting-to-params-changes>

first define a new page component

```js

const GamePage = Vue.component('Game', require('./pages/Game.vue').default);

```

then a new route

```js
    {
        path: '/games/:id',
        name: 'game',
        component: GamePage
    },

```

Then the component page definition where we will make a new axios call for the single game resource using `this.$route.params.id` to get the route parameter.

```vue
<template>
    <div>
        <img :src="'/storage/' + game.thumbnail" alt />
        <div class="card-body">
            <h3>{{ game.name }}</h3>
        </div>
    </div>
</template>

<script>import Axios from "axios"

export default {

    data() {
        return {
            game: null
        }
    },
    mounted() {
        // Call the api for the single post
        Axios.get('/api/games/' + this.$route.params.id).then(resp => {
            console.log(resp);
            this.game = resp.data.data;
        })
    }

}
</script>

<style>
</style>

```

To perform this call we need an endpoint like so

```php
Route::get('games/{game}', 'API\GameController@show');
```

The show method

```php
   public function show(Game $game)
    {
        //ddd($game->id);
        $thisGame = Game::where('id', $game->id)->with('category')->first();
        //ddd($thisGame);
        return new GameResource($thisGame);
    }
```

Did you notice that if we put the wrong game id in the route we won't get a 404?
We can use a catch * route also for vue-router <https://router.vuejs.org/guide/essentials/dynamic-matching.html#catch-all-404-not-found-route>

```js

    {
        path: '*',
        component: _404
    }
```

and define a custom component `_404`

```js
const _404 = Vue.component('404', require('./pages/404.vue').default);
```

next build page template

```vue
<template>
  <h1>😱404! Can't find what you are looking for </h1>
</template>

<script>
export default {

}
</script>

<style>

</style>
```

If something doesn't work, debug and fix it.

DONE! You now have an hybrid spa application that runs on laravel.
