# Laravel SPA - Pagination

First let's print the resources in descending order.
Update the index method in the API/GameController

```php
return GameResource::collection(Game::with(['category'])->orderByDesc('id')->paginate(5));
```

Next create a basic markup where show the pagination, inside the Games.vue page component

```html
 <div class="pagination">
    <span class="btn text-secondary">Prev</span>
    <span class="btn btn-primary">{{meta.current_page}}</span>
    <span class="btn text-secondary">Next</span>
</div>
```

Now that we have a basic markup for our pagination let's define what methods we want to call to trigger a page change.

```js
nextPage(){
    console.log('Clicked on next page');
},
prevPage(){
    console.log('Clicked on prev page');

}
```

then we update the markup

```html

<div class="pagination" v-if="!loading">
    <span class="btn text-secondary" @click='prevPage()'>Prev</span>
    <span class="btn btn-primary">{{meta.current_page}}</span>
    <span class="btn text-secondary" @click='nextPage()'>Next</span>
</div>
```

Change the fetchGames method

```js

fetchGames(url) {
    axios.get(url).then((response) => {
        //console.log(response);
        this.games = response.data.data;
        this.meta = response.data.meta;
        this.links = response.data.links;
        this.loading = false;
    });
},
```

Implement the next method

```js
nextPage(){
    console.log('Clicked on next page', this.links.next);
    this.fetchGames(this.links.next)
},
```

Imprement the prev method

```js

prevPage(){
    console.log('Clicked on prev page');
    this.fetchGames(this.links.prev)
}
```

Now, hide the prev and next elements if we are on the first or last pages

```html
 <span class="btn text-secondary" @click='prevPage()' v-if="meta.current_page > 1">Prev</span>
```

and the next

```html
<span class="btn text-secondary" @click='nextPage()' v-if="meta.current_page !== meta.last_page">Next</span>
```

If you want to make the pagination more user friendly, we can add links to go to the last page or go back to the first page as well.

Update the markup to generate all page nubers dynamically
and attach them an onclick event.

```html
<span class="btn" :class="n === meta.current_page ? 'btn-primary' : ''" v-for="n in meta.last_page" @click="goToPage(n)">{{n}}</span>
```

next define the method

```js
goToPage(page_number){
    this.fetchGames("api/games?page=" + page_number)
}

```

Done
