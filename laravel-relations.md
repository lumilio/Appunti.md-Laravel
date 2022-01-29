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

## Show all posts associated with a tag

We need a route that uses a category paramenter and call an action that returns all posts of the given category.

```php
Route::get('categories/{category:slug}/posts', 'CategoryController@posts')->name('categories.posts');
```

Inside the CategoryController the post method implementation could be the following:

```php
    public function posts(Category $category)
    {
        $posts = $category->posts()->paginate(12);
        return view('guest.categories.posts', compact('posts', 'category'));
    }
```

And finally its view in views/categories/posts.blade.php

```html
<div class="p-5 bg-light">
    <div class="container">
        <h1 class="display-3">Category: {{$category->name}}</h1>

    </div>
</div>


<div class="container">
    <div class="row">
        @forelse($posts as $post)
        <div class="col-md-4">
            <div class="card">
                <img class="card-img-top" src="{{$post->cover}}" alt="">
                <div class="card-body">
                    <h4 class="card-title">{{$post->title}}</h4>
                    <p class="card-text">{{$post->sub_title}}</p>
                </div>
            </div>
        </div>
        @empty
        <div class="col">
            <p>Sorry nothing to show here 😱</p>
        </div>
        @endforelse

    </div>
    {{ $posts->links() }}
</div>

```

### We could add an admin page to manage categories

In the admin/categories/index.blade.php we could show a list of categories, a form to create a new category and a button to remove a given category.

```php
## to do
```


## One to Many | A User has many Posts <-> A post Belongs to a User

- Follow the same steps described above to setup the relationship exept the implementation of the select tags.
- restict posts access to the post author

### The Has many relationship on the user model

```php

    /**
     * Get all of the posts for the User
     *
     * @return \Illuminate\Database\Eloquent\Relations\HasMany
     */
    public function posts(): HasMany
    {
        return $this->hasMany(Models\Post::class);
    }
```

### The inverse in the Post model

```php

 /**
     * Get the user that owns the Post
     *
     * @return \Illuminate\Database\Eloquent\Relations\BelongsTo
     */
    public function user(): BelongsTo
    {
        return $this->belongsTo(Models\User::class);
    }
```

### Assign all posts to the current logged in user.

```php
php artisan tinker
// find the first user
$user = User::find(1)
// find all posts
$posts = Post::all();
// Associate many posts to the first user
$user->posts()->saveMany($posts)
```

### Restrict access to posts to the author 

Only the author of a post can see all his posts

In the Admin/PostController.php

```php
  public function index()
    {
        $posts = Auth::user()->posts()->orderByDesc('id')->paginate(10);
        return view('admin.posts.index', compact('posts'));
    }

```

The code above uses the `Auth` facades to grab the current user's posts and paginate them before returning the view.

next when we create a post we need to add to the  validated data also the user_id

```php
  $validated['user_id'] = Auth::id();
```

In the edit method we need to check if the user who requests the view is the same user who wrote the post otherwise we abort.

```php

public function edit(Post $post)
    {
        $categories = Category::all();
        $tags = Tag::all();
        if (Auth::id() === $post->user_id) {
            return view('admin.posts.edit', compact('post', 'categories', 'tags'));
        } else {
            abort(403);
        }
    }
```

The same is true for the Update method

```php
public function update(Request $request, Post $post)
{
    //ddd($request->all());
    if (Auth::id() === $post->user_id) {
        //ddd($request->all());
        $validated = $request->validate([
            'title' => ['required', Rule::unique('posts')->ignore($post->id), 'max:200'],
            'sub_title' => 'nullable',
            'cover' => 'nullable',
            'body' => 'nullable',
            'category_id' => 'nullable|exists:categories,id',
            'tags' => 'nullable|exists:tags,id'
        ]);

        $validated['slug'] = Str::slug($validated['title']);
        //ddd($validated);
        $validated['user_id'] = Auth::id();
        $post->update($validated);
        if ($request->has('tags')) $post->tags()->sync($validated['tags']);

        return redirect()->route('admin.posts.index');
    } else {
        abort(403);
    }
}
```

And you need to do the same in the destroy method.
If you don't we can hack the form and delete a post that belongs to another user.

```php
if(Auth::id() === $post->user_id){
      public function destroy(Post $post)
    {
        $title = $post->title;
        $post->delete();
        return redirect()->back()->with('message', "Success! Post $title Deleted ");
    }
} else {
  abort(403)
}
```

## One to One

TODO

## Many to Many

Add many to many relationship between tags and posts

## Step 1
create methods in Post and Tag models

in Tag.php
```php
    /**
     * The posts that belong to the Tag
     *
     * @return \Illuminate\Database\Eloquent\Relations\BelongsToMany
     */
    public function posts()
    {
        return $this->belongsToMany(Post::class);
    }
```
in Post.php

```php
    /**
     * The tags that belong to the Post
     *
     * @return \Illuminate\Database\Eloquent\Relations\BelongsToMany
     */
    public function tags()
    {
        return $this->belongsToMany(Tag::class);
    }
```


## Step 2. Create migration for the pivot table

```bash
php artisan make:migration create_post_tag_table
```

## Step 3. Add foreing keys to the pivot table

```php
 Schema::create('post_tag', function (Blueprint $table) {
            
    $table->unsignedBigInteger('post_id');
    $table->foreign('post_id')->references('id')->on('posts');
    $table->unsignedBigInteger('tag_id');
    $table->foreign('tag_id')->references('id')->on('tags');
    $table->primary(['post_id', 'tag_id']);

});
```

## Step 4. run the migration

```bash

php artisan migrate
```

## Step 5.

Seed the tags table
```php
use Illuminate\Database\Seeder;
use Faker\Generator as Faker;
class TagSeeder extends Seeder
{
    /**
     * Run the database seeds.
     *
     * @return void
     */
    public function run(Faker $faker)
    {
        for ($i=0; $i < 10; $i++) { 
            $newTag = new App\Tag();
            $newTag->name = $faker->word(); 
            $newTag->save();
        }
    }
}

```
run the seeder
```
php artisan db:seed --class=TagSeeder
```

## Step 6. Attach and detach 
Attach tags to posts via tinker
```bash
php artisan tinker

// show all tags
Tag::all()
// take the first tag
$tag = Tag::first()
// show all posts associated with a tag
$tag->posts
// attach to the tag the post with and id of 1
$tag->posts()->attach(1)

// Do the same starting from a post
// show all posts
Post::all()
// take the first tag
$post = Post::first()
// show all posts associated with a tag
$post->tags
// attach to the tag the post with and id of 1
$post->tags()->attach(1)

// Attach other posts to other tags
$post = Post::find(2)
$post->tags()->attach(2)
$post->tags()->attach(3)

// Detach from a post all tags
$post->tags()->detach()
// Detach from a post a tag - only incase has other tags attached
$post->tags()->detach(1)
```

## step 7. Add tags to the Posts CRUD (create/store)
### show all tags attached to a post in the post.show view
```html
<div class="tags">
        tags:
        @if(count($post->tags) > 0) 
            @foreach($post->tags as $tag)
                {{$tag->name}}
            @endforeach
        @else 
            <span>No tags</span>

        @endif
   
    </div>
    
```

### Add tags to a post when it's created (PostController.php)
Change the create method on the Post controller and returns all tags in the db

in the PostController@create method
```php
public function create()
{
    //get all tags
    $tags = Tag::all();
    //dd($tags);
    return view('posts.create', compact('tags'));
}
```

A Multiple select needs a name=tags[] to return multiple elements
posts/create.blade.php
```html

 <div class="form-group">
    <label for="tags">Tags</label>
    <select multiple class="form-control" name="tags[]" id="tags">
        @if($tags)
            @foreach($tags as $tag)
                <option value="{{$tag->id}}">{{$tag->name}}</option>
            @endforeach
        @endif
    </select>
    </div>
    @error('tags')
        <div class="alert alert-danger">{{ $message }}</div>
    @enderror
```


### Attach tags to the post via the store method
```php
    public function store(Request $request)
    {
        //dd($request->all()); // get the request
        //dd($request->tags); // get all tags

        // validare i dati
        $validatedData = $request->validate([
           'title' => 'required',
           'body' => 'required' 
        ]);
       $new_post = Post::create($validatedData);
        
        
        // attach all tags to the post
        $new_post->tags()->attach($request->tags);
        
        return redirect()->route('posts.show', $new_post);
    }
```

### Validate tags before storing them in the database

Hack the form and add a tag that does not exit to prove db issue then
Add the tags validation like so.

```php
    $validatedData = $request->validate([
        'title' => 'required',
        'body' => 'required',
        'tags'=>'exists:tags,id'
    ]);
```

## Step 8. Add tags to the CRUD (edit/update)

add select form field to the edit form

```html
<div class="form-group">
<label for="tags">Tags</label>
<select multiple class="form-control" name="tags[]" id="tags">
    @if($tags)
        @foreach($tags as $tag)
            <option value="{{$tag->id}}" {{$post->tags->contains($tag) ? 'selected' : ''}}>{{$tag->name}}</option>
        @endforeach
    @endif
</select>
</div>
@error('tags')
    <div class="alert alert-danger">{{ $message }}</div>
@enderror

```

### update and validate

```php
    public function update(Request $request, Post $post)
    {
        
        //dd($request->tags); // check the tags first with a dd
        
        // validare i dati
         $validatedData = $request->validate([
           'title' => 'required',
           'body' => 'required',
           'tags' => 'exists:tags,id' //validate tags
        ]);

        //update post data
        $post->update($validatedData); 

        // Update tags with sync
        $post->tags()->sync($request->tags);
        return redirect()->route('posts.index');
    }
```

