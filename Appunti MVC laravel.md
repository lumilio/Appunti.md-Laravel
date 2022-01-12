# Laravel 607 - Model and Controller

This will guide you through the steps required to start using the MVC pattern in laravel
create controllers and models to interact with data in your database.

We will:

- Install laravel
- install npm dependencies
- create a database, a table and add some records
- configure laravel to access our database
- create a layout and an homepage

## Install laravel

```bash
composer require laravel/laravel --prefer-dist=^7.0 .

```

## Install npm dependencies

```bash
npm i
```

## Create a database and a books table with records

Use the GUI or the command line to add a db and a table, then insert a couple of records inside the books table.

```bash
mysql -uyour_user -p
> create database 42_library;
exit;
```

next create a table via PHPMyAdmin and add records.

## Connect Laravel with the database

Edit .env and setup your credentials to access the database

```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3307 #<-- db port number - default 3306 unless you changed it. 
DB_DATABASE=42_library # <-- Your laravel database name
DB_USERNAME=fab # <-- Your db username  
DB_PASSWORD=password # <-- Your db password
```

## Start artisan serve

```php artisan serve```

## Create a layout, an home view and its route

First update the route definition inside routes/web.php and point the home route '/' to an home view

```php
Route::get('/', function () {
    return view('home');
});
```
next create a layout file, rename the welcome view and extend the app layout.

```bash
# enter the views folder
cd /resources/views
# create a layouts directory
mkdir layouts#
# create an empty php file
touch app.blade.php
```
Use the command line (the commands above work on linux on git bash on Windows)
or VScode to create files and folders.

Inside app.blade.php let's create the layout
```html
<!DOCTYPE html>
<html lang="{{ str_replace('_', '-', app()->getLocale()) }}">

<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">

    <title>Laravel @yield('title')</title>

    <!-- Fonts -->
    <link href="https://fonts.googleapis.com/css2?family=Nunito:wght@200;600&display=swap" rel="stylesheet">

</head>

<body>
    @yield('content')
</body>

</html>
```

Now we can use this layout and extend it inside the home view.

Inside views/home.blade.php

```php

@extends('layouts.app')

@section('content')

<div class="container">
    <h1>Devs Library</h1>
    <p>Lorem ipsum dolor sit amet consectetur adipisicing elit. Voluptates, inventore.</p>

    <section class="books">
        <p>Check out books selection</p>
    </section>
</div>

@endsection
```

Now our view is ready. The next steps are:
- create a controller 
- migrate the logic from the route closure to a controller method
- create a book model
- render the list of books in the home view


## Create a controller
we will use `php artisan` to create a controller and then we will our code from the closure to a method in the contronner.

```bash
# check the docs for php artisan make:controller
php artisan help make:controller
# now we know how to use it, lets create a page controller
php artisan make:controller PageController

```
Artisan will create the controller for us in the right direcory and put some code in it.

This is how the new controller looks out of the box

```php

<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class PageController extends Controller
{
    //
}
```

## Migrate our logic from the closure to the controller
Our routes file web.php uses closure functions to return a view to the user after a get request. But that should be a job for our `PageController`.

The PageController is just a class, inside it we will put a method that does the same job of the closure, then call this method from the route instead of using the closure function.

```php

class PageController extends Controller
{
    // Add an index method to manage requests from the / route
    public function index()
    {
        return view('home');
    }
}

```
Inside our index method we do exactly the same things we used to do in the Route closure, we return the home view! that's it for now.

Next we need to update our route and tell it to use our controller.
```php
Route::get('/', 'PageController@index');
```
Done! refresh the page and everything should work as expected.🥳


## Render all books
Now the fun part. Let's create a model that maps our database books table so we can
return all books inside the home view!


```bash

# check the docs for php artisan make:controller
php artisan help make:model
# now we know how to use it, lets create a page controller
php artisan make:model Book

```

🧙‍♂️ That's all there is to do, it works like magic.

Now that we have a model we can grab all associtated records using laravel eloquent functions.

We need to do the following: 
- Import the model at the top of the Controller
- use the model name and a static method called `all()` to retrive all results from the database.

```php
namespace App\Http\Controllers;

use App\Models\Book; # <-- Import the model ⚡
use Illuminate\Http\Request;

class PageController extends Controller
{
    //Manage the requests for / route
    public function index()
    {
        ddd(Book::all()); # <-- Call the static method all()
        return view('home');
    }
}

```

As you can see, if you did everything correctly, the die dump and debug has two items, and inside attributes you can see our data.

Lets render all books now.

update the controller and pass the collection of items to the view
```php

public function index()
  {
      //ddd(Book::all());
      $books = Book::all();        
      return view('home', compact('books'));
  }
```

And finally inside the home.blade.php view
```php

@forelse ($books as $book)

<div class="book">
    <h3>{{$book->title}}</h3>
    <div class="description">
        <p>{{$book->plot}}</p>
    </div>
</div>

@empty
   <p class="alert"> Sorry no books available</p>
@endforelse
```

