# Laravel Relationships

## One to Many (Post & Category)

In un blog una relazione one to many puó esserci tra un articolo ed una categoria.
Un articolo puó essere associato ad una categoria.
Una categoria puó avere molti articoli.

### Definire la Categoria

Creiamo un modello per la Categoria, con migrazione, il controller e il seeder

```bash
php artisan make:model -rcsm Category
```

Definiamo la struttura della categoria

```php
Schema::create('categories', function (Blueprint $table) {
  $table->id();
  $table->string('name');
  $table->string('slug');
  $table->timestamps();
});
```

Definiamo il seeder

```php
$categories = ['Programming', 'Automation', 'Web design', 'Best Practices'];
foreach ($categories as $category) {
    $cat = new Category();
    $cat->name = $category;
    $cat->slug = Str::slug($category);
    $cat->save();
}
```

Migriamo la tabella e facciamo il seeding

```bash
php artisan migrate
php artisan db:seed --class=CategorySeeder
```

### Creiamo una relazione one to many

Un post puó essere associato ad una categoria, quindi possiamo dire che un post 'appartiene ad una' categoria. A post belongsTo a category.

nel modello Post.php

```php
public function category(): BelongsTo
{
    return $this->belongsTo(Category::class);
}
```

Define the inverse on Category.php

```php
public function posts(): HasMany
{
    return $this->hasMany(Post::class);
}
```

### Impostiamo la chiave esterna sulla tabella secondaria

Tra i posts e le categories la tabella indipendente é quella delle categorie, quindi nella tabella dei posts bisogna aggiungere la foreign key category_id che punta all'id della tabella categories.

Creaiamo una nuova migrazione:

```bash
php artisan make:migration add_category_id_to_posts_table
```

Implementiamo la migrazione

```php
public function up()
{
  Schema::table('posts', function (Blueprint $table) {
      $table->unsignedBigInteger('category_id')->nullable()->after('id');
      $table->foreign('category_id')->references('id')->on('categories')->onDelete('set null');
  });
}

public function down()
{
  Schema::table('posts', function (Blueprint $table) {
      $table->dropForeign('posts_category_id_foreign');
      $table->dropColumn('category_id');
  });
}

```

Migriamo la tabella `php artisan migrate`

### Inseriamo e aggiorniamo i modelli di una relazione

[Documentation](https://laravel.com/docs/7.x/eloquent-relationships#inserting-and-updating-related-models)

Tramite tinker `php artisan thinker` creiamo delle associazioni tra posts e categorie e viceversa.

Aggiungiamo un post ad una categoria

```php
//Seleziona post 
$post = App\Post::find(20)
//Seleziona Categoria
$category App\Category::find(4)
// Assgna il post alla categoria
$cat->posts()->save($post);
//Verifichiamo
$cat->posts

```

Assegnamo una categoria ad un post

```php
$cat2 = App\Category::find(2);
$post = App\Post::find(24)
$post->category()->associate($cat2)
//Verifichiamo
$post->category
```

Assegnato tutti i posts ad una categoria

```php
// Selezioniamo alcuni posts
$posts = App\Post::where('id', '>', 25)->get();
// Selezioniamo una categoria
$cat3 = App\Category::find(3);
// Associamo i posts all categoria
$cat3->posts()->saveMany($posts);
// Verifichiamo
$cat3->posts
```

### Aggiungiamo la relazione al CRUD

nell'Admin/PostController aggiungiamo al metodo create anche una select per mostrare le categorie disponibili.

Aggiungiamo alle fillable properties del modello Post, category_id

```php
protected $fillable = ['title', 'image', 'body', 'category_id'];
```

Modifichiamo il metodo create dell'Admin/PostController

```php
public function create()
{
    $categories = Category::all();
    return view('admin.posts.create', compact('categories'));
}

```

Mostriamo il select nel form

```html

<div class="form-group">
  <label for="category_id">Categories</label>
  <select class="form-control" name="category_id" id="category_id">
      <option selected disabled>Select a category</option>
      @foreach($categories as $category)
      <option value="{{$category->id}}">{{$category->name}}</option>
      @endforeach

  </select>
</div>

```

Dumpiamo i dati nel metodo store e vediamo che otteniamo

```php
ddd($request->all());
```

Aggiungiamo una regola di validazione per la categoria ricevuta dall'utente

```php
 'category' => 'nullable|exists:categories,id'
```

Create the post and associate the category

```php
Post::create($validateData);
return redirect()->route('admin.posts.index');
```

Aggiorniamo il select al form edit e aggirniamo il metodo edit

```html

<div class="form-group">
  <label for="category_id">Categories</label>
  <select class="form-control" name="category_id" id="category_id">
      <option value="">Select a category</option>
      @foreach($categories as $category)
      <option value="{{$category->id}}" {{$category->id == old('category', $post->category_id) ? 'selected' : ''}}>{{$category->name}}</option>
      @endforeach

  </select>
</div>
```

Aggiungiamo alla validazione category_id nel metodo update

```php
'category_id' => 'nullable | exists:categories,id',
```

Mostriamo le categorie nei post, posts.show view

```php
<em>Category: {{ $post->category ? $post->category->name : 'Uncategorized'}}</em>
```

## One to One

TODO

## Many to Many

TODO
