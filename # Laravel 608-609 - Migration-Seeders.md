# Laravel 608 - 609 - Migrations and Seeders

- Project Setup
- Define Routes
- Create Model-Controller-Migration
- Database seeding

## Project Setup

You need to do the usual tasks before we can start using migrations and seeders. which means:

### install laravel `composer require laravel/laravel --prefer-dist=7.0`

### Connect Laravel with the database

Edit .env and setup your credentials to access the database

```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1

DB_PORT=3307 #<-- db port number - default 3306 unless 
you changed it. 

DB_DATABASE=your_database_name_here # <-- Your laravel database name

DB_USERNAME=your_user_name # <-- Your db username, MAMP default is root  

DB_PASSWORD=your_password # <-- Your db password, MAMP default root
```

### Setup a layout file

```bash
cd resources/views
mkdir layouts
cp welcome.blade.php layouts/app.blade.php
```

### Next open the app.blade.php file define the layout structure inside the body tag

```html
<header><!-- Your header code here --></header>
<main>
  <!-- Yield the main page content -->
  @yield('content')
</main>
<footer>
  <!-- Your footer code here -->
</footer>

```

Remember to add the css link and js script in the head 

```html
<head>
  <!-- Styles -->
  <link rel="stylesheet" href="{{asset('css/app.css')}}">

<!-- Wait the page is fully loaded to load our script using 'defer' -->
 <script src="{{asset('js/app.js')}}" defer></script>
</head>

```

### Configure laravel Mix

Add to the Mix file the methods to copy images and optios to url processing

```js

mix.js('resources/js/app.js', 'public/js')
.copyDirectory('resources/img', 'public/img')
.options({
    processCssUrls : false
})
.sass('resources/sass/app.scss', 'public/css');


```

Next we can install bootstrap

```npm i bootstrap```

then inside app.scss `@use 'bootstrap'` and inside app.js `window.bootstrap = require('bootstrap')`

## Define Your Routes

Now let's define some routes for a real estete, we will start with a route to list all houses and one for the single house.

```php
Route::get('houses', 'HouseController@index')->name('houses');
Route::get('houses/{house}', 'HouseController@show')->name('house');

```

next, we need to define a controller a model for the house and a migration.

## Define Controllers, Models and Migrations

We can use `php artisan make:model -a House` to scaffold a bounch of files we will need in a moment.

The `-a` flag will create:

- model
- controller
- migration
- seeder
- factory


Before moving forward, if you used the option `-a` laravel 7 will place the model inside `app/`, let's move it in a dedicated folder called `Models` and update its namespace

```bash
cd app
mkdir Models
mv House.php Models/House.php 

```

then edit House.php and update the namespace

```php
namespace App\Models;

```

now we can define the blueprint for our houses table, and update the migration.

A house has:

- id (AI, PK, NN, UN)
- address (string)
- city (string)
- zip_code (char)
- number_of_rooms (tiny int)
- square_meters (tiny int)
- price (float)
- to_let (boolean default 1)
- for_sale (boolean default 1)

### Edit the migration and migrate the db

```php

# Inside the schema

Schema::create('houses', function (Blueprint $table) {
    $table->id();
    $table->string('address')->nullable();
    $table->string('city');
    $table->char('zip_code', '6')->nullable();
    $table->string('country')->default('Italy');
    $table->string('county')->nullable();
    $table->smallinteger('square_meters', false , true);
    $table->tinyInteger('rooms')->nullable();
    $table->string('type', 100)->nullable();
    $table->boolean('for_sale')->default(0);
    $table->boolean('to_let')->default(1);
    $table->boolean('is_available')->default(1);
    $table->decimal('price', 8, 2, true);
    $table->timestamps();
});

```

Now let's migrate the db and add one house using tinker.

```bash

php artisan tinker

```

With lots of columns in a table tinker becomes not very practical, that's when seeders comes into play.

## Database Seeders

Inside `database/seeds` laravel has created a new file called `HouseSeeder.php` this class has a `run()` method that we can use to populate our database table.

We can either create a basic data structure and use it to feed the database or use an external library to insert fake data. Let's see both.

### Define an array of associative arrays and feed the database

Inside HouseSeeder.php let's edit the `run` method and define a houses structure.

```php

public function run()
{
  // Define the data
    $houses = [
      [
        'address' => '',
        'city' => '',
        'zip_code' => '',
        'country' => '',
        'county' => '',
        'square_meters' => '',
        'rooms' => '',
        'type' => '',
        'for_sale' => '',
        'to_let' => '',
        'is_available' => '',
        'price' => ''
      ], 
      # Fill the data, then copy paste to add more entries.
    ]

    # loop over tue array
}

```

Once our houses structure is ready we can loop over it and create multiple instances of the `House` model and insert them into the db.

Remember to import the House model at the top of the seeder.

Inside HouseSeeder.php

```php

use App\Models\House;

```

Next use a for each to insert the data

```php

// Define the data

$houses = [
        [
            'address' => '5th avenue',
            'city' => 'New York City',
            'zip_code' => 'NYC-001',
            'country' => 'USA',
            'county' => 'New York',
            'square_meters' => 200,
            'rooms' => 5,
            'type' => 'Villa',
            'for_sale' => true,
            'to_let' => false,
            'is_available' => true,
            'price' => 400000
        ],      [
            'address' => 'central park boulevard',
            'city' => 'New York City',
            'zip_code' => 'NYC-005',
            'country' => 'USA',
            'county' => 'New York',
            'square_meters' => 300,
            'rooms' => 7,
            'type' => 'Palace',
            'for_sale' => true,
            'to_let' => false,
            'is_available' => true,
            'price' => 600000
        ],
        [
            'address' => 'Brookleen road',
            'city' => 'Brookleen',
            'zip_code' => 'BRK-007',
            'country' => 'USA',
            'county' => 'New York',
            'square_meters' => 100,
            'rooms' => 3,
            'type' => 'Apartment',
            'for_sale' => false,
            'to_let' => true,
            'is_available' => true,
            'price' => 1000
        ],

    ];


# Loop over the array and save the data into the db

foreach($houses as $house) {
    $_house = new House(); # <-- remember to create a new instance otherwise you will get an error.
    $_house->address = $house['address'];
    $_house->city = $house['city'];
    $_house->zip_code = $house['zip_code'];
    $_house->country = $house['country'];
    $_house->county = $house['county'];
    $_house->square_meters = $house['square_meters'];
    $_house->rooms = $house['rooms'];
    $_house->type = $house['type'];
    $_house->for_sale = $house['for_sale'];
    $_house->to_let = $house['to_let'];
    $_house->is_available = $house['is_available'];
    $_house->price = $house['price'];
    $_house->save(); # <-- remember to save otherwise nothing will be inserted into the database.

}
     
```

Now we can use `php artisan db:seed --class=HouseSeeder` to populate our database.

### Use Faker

We can use a library called faker to add data to the db via the seeder.

First you need to import the Faker/Generator class at the top of the seeder

```php
use Faker/Generator as Faker;
use App/Models/House;
```

then use the Faker alias and assign inject the instance inside the `run()` method

```php

  public function run(Faker $faker)
    {
      # use a for loop to create x amount of records 

    for ($i = 0; $i < 10; $i++)
      $new_item = new House(); # <-- remember to create a new instance otherwise you will get an error.
      $new_item->property = $faker->sentence;
      # ... set up all required properties for the new instance, then save.
      $new_item->save() # <-- remember to save otherwise nothing will be inserted into the database.

    }

```
