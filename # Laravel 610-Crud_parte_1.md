# Laravel Crud Parte 1

- Project setup
- Creazione 7 restful routes
- Creazione Resource Route
- Creazione Modello
- Creazione Controller resource

Inside a Laravel Application we often want users (at least admin users) to be able to perform specific operations on each resource.

If we want to create a blog we have a Post model, when we retrive posts from the database we call them resources on which we perform seven standard operations known as CRUD.

C = Create
R = Read
U = Update
D = Delete

## Define the seven restful routes

Routes responds to http methods as GET, POST, PUT/PATCH, DELETE.

Resources can be shown, created, updated and deleted. In laravel we define seven routes that allows us to perform these operations.

- GET /posts - A get request to the posts uri will return a list of all posts
- GET /posts/create - A get request to this endpoint will show a form that we can use to create a new resource
- POST /posts - A post request to this endpoint will save the resource inside the database
- GET /posts/{post} - A get request to a route with a parameter is used to show a single resource
- GET /posts/{post}/edit - a get request to this endpoint will show a form filled with data for the given resource
- PUT or PATCH /posts/{post} - a put or patch request to this endpoint will let us update a given resource
- DELETE /posts/{post} - a delete request to this endpoint is used to delete a given resource.

The above restful routes can be defined inside the route file web.php as shown below:

```php
# Seven restful routes to perform CRUD operations
// Show a list of all posts
Route::get('posts', 'Admin\PostController@index');
Route::get('posts/create', 'Admin\PostController@create');
Route::post('posts', 'Admin\PostController@store');
Route::get('posts/{post}', 'Admin\PostController@show');
Route::get('posts/{post}/edit', 'AdminnPostController@edit');
Route::put('posts/{post}', 'Admin\PostController@update');
Route::delete('posts/{post}', 'Admin\PostController@destroy');

```

Note: pay attention to the routes order.

## Creazione Route resource

```php

Route::resouce('posts', 'PostController');

```

## Creazione modello

```bash

php artisan make:model ModelName
```

## Creazione controller di tipo resource

Controller abbinato a modello.

```bash
php artisan make:controller PostController -r -m Post

```
