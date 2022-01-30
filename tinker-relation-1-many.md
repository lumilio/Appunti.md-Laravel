```
lumilio@Air-di-Lorenzo laravel-boolpress-4 % php artisan tinker
Psy Shell v0.11.1 (PHP 7.4.21 — cli) by Justin Hileman
>>> Category::all();
[!] Aliasing 'Category' to 'App\Models\Category' for this Tinker session.
=> Illuminate\Database\Eloquent\Collection {#4374
     all: [
       App\Models\Category {#4375
         id: 1,
         name: "Attulità",
         slug: "attulita",
         created_at: "2022-01-30 12:10:00",
         updated_at: "2022-01-30 12:10:00",
       },
       App\Models\Category {#4376
         id: 2,
         name: "Intrattenimento",
         slug: "intrattenimento",
         created_at: "2022-01-30 12:10:00",
         updated_at: "2022-01-30 12:10:00",
       },
       App\Models\Category {#4377
         id: 3,
         name: "Sport",
         slug: "sport",
         created_at: "2022-01-30 12:10:00",
         updated_at: "2022-01-30 12:10:00",
       },
       App\Models\Category {#4378
         id: 4,
         name: "Animazione",
         slug: "animazione",
         created_at: "2022-01-30 12:10:00",
         updated_at: "2022-01-30 12:10:00",
       },
       App\Models\Category {#4379
         id: 5,
         name: "Documentari",
         slug: "documentari",
         created_at: "2022-01-30 12:10:00",
         updated_at: "2022-01-30 12:10:00",
       },
     ],
   }
>>> Post::all()
[!] Aliasing 'Post' to 'App\Models\Post' for this Tinker session.
=> Illuminate\Database\Eloquent\Collection {#4390
     all: [
       App\Models\Post {#4391
         id: 1,
         category_id: null,
         cover: "https://via.placeholder.com/600x400.png/00bb11?text=Products+qui",
         description: "Aliquid animi minus cupiditate necessitatibus officiis inventore at voluptatibus. Qui optio consequatur repellat vel. Aut sint et iusto corrupti sit et sed.",
         slug: "httpsviaplaceholdercom600x400png00bb11textproductsqui",
         created_at: "2022-01-30 12:10:04",
         updated_at: "2022-01-30 12:10:04",
       },
       App\Models\Post {#4392
         id: 2,
         category_id: null,
         cover: "https://via.placeholder.com/600x400.png/0044cc?text=Products+assumenda",
         description: "Vitae fuga et in ut deleniti aut. Enim porro optio et expedita. Et fuga iure voluptatum.",
         slug: "httpsviaplaceholdercom600x400png0044cctextproductsassumenda",
         created_at: "2022-01-30 12:10:04",
         updated_at: "2022-01-30 12:10:04",
       },
       App\Models\Post {#4393
         id: 3,
         category_id: null,
         cover: "https://via.placeholder.com/600x400.png/002200?text=Products+quia",
         description: "Ut esse ut magnam rerum. Animi ipsum necessitatibus modi quisquam quod voluptatem dolorem. Nobis voluptatum quisquam est doloremque.",
         slug: "httpsviaplaceholdercom600x400png002200textproductsquia",
         created_at: "2022-01-30 12:10:04",
         updated_at: "2022-01-30 12:10:04",
       },
       App\Models\Post {#4394
         id: 4,
         category_id: null,
         cover: "https://via.placeholder.com/600x400.png/0022ee?text=Products+illum",
         description: "Harum non asperiores quia debitis reprehenderit ratione. Eum harum consequatur voluptatibus ad. Ea ea aperiam repellat aspernatur. Est est fugiat animi enim minus deleniti.",
         slug: "httpsviaplaceholdercom600x400png0022eetextproductsillum",
         created_at: "2022-01-30 12:10:04",
         updated_at: "2022-01-30 12:10:04",
       },
       App\Models\Post {#4395
         id: 5,
         category_id: null,
         cover: "https://via.placeholder.com/600x400.png/00ccaa?text=Products+voluptatum",
         description: "Et libero officia culpa minima iure non. Quo iusto non nam nam. Sint ex perspiciatis autem deleniti ea expedita autem. Quisquam in atque fugit quos quasi perspiciatis.",
         slug: "httpsviaplaceholdercom600x400png00ccaatextproductsvoluptatum",
         created_at: "2022-01-30 12:10:04",
         updated_at: "2022-01-30 12:10:04",
       },
       App\Models\Post {#4396
         id: 6,
         category_id: null,
         cover: "https://via.placeholder.com/600x400.png/00aa33?text=Products+impedit",
         description: "Quia ipsum tempora explicabo. Doloremque impedit alias nam dolorem dignissimos similique facilis nulla. Eos dolor recusandae dolorem nam odit quis maxime.",
         slug: "httpsviaplaceholdercom600x400png00aa33textproductsimpedit",
         created_at: "2022-01-30 12:10:04",
         updated_at: "2022-01-30 12:10:04",
       },
       App\Models\Post {#4397
         id: 7,
         category_id: null,
         cover: "https://via.placeholder.com/600x400.png/0000aa?text=Products+nam",
         description: "Reiciendis veritatis nobis temporibus aut occaecati cupiditate impedit. Ea molestiae est recusandae aliquam in reiciendis. Natus illo et nemo sit atque alias rem.",
         slug: "httpsviaplaceholdercom600x400png0000aatextproductsnam",
         created_at: "2022-01-30 12:10:04",
         updated_at: "2022-01-30 12:10:04",
       },
       App\Models\Post {#4398
         id: 8,
         category_id: null,
         cover: "https://via.placeholder.com/600x400.png/00ee33?text=Products+eos",
         description: "Nihil consequatur minima vel cum officiis adipisci iste. Est nisi culpa aut similique laudantium unde asperiores.",
         slug: "httpsviaplaceholdercom600x400png00ee33textproductseos",
         created_at: "2022-01-30 12:10:04",
         updated_at: "2022-01-30 12:10:04",
       },
       App\Models\Post {#4399
         id: 9,
         category_id: null,
         cover: "https://via.placeholder.com/600x400.png/005511?text=Products+sed",
         description: "Et quam sunt quo corrupti. Aut optio officiis eum vel debitis beatae. Voluptatem consequatur voluptas et dolorem minus quis. Dolorum optio illum dolorum rerum. Sed vel distinctio aut eligendi.",
         slug: "httpsviaplaceholdercom600x400png005511textproductssed",
         created_at: "2022-01-30 12:10:04",
         updated_at: "2022-01-30 12:10:04",
       },
       App\Models\Post {#4400
         id: 10,
         category_id: null,
         cover: "https://via.placeholder.com/600x400.png/002211?text=Products+distinctio",
         description: "Quia quam id sit. Nam iusto debitis officiis quasi. In incidunt maiores eos vero facere ea. Est soluta voluptatem doloribus ut et expedita dignissimos. Voluptatibus aut ut suscipit error odio nam.",
         slug: "httpsviaplaceholdercom600x400png002211textproductsdistinctio",
         created_at: "2022-01-30 12:10:04",
         updated_at: "2022-01-30 12:10:04",
       },
       App\Models\Post {#4401
         id: 11,
         category_id: null,
         cover: "xxx",
         description: "lorem ipsum",
         slug: "xxx",
         created_at: "2022-01-30 12:11:20",
         updated_at: "2022-01-30 12:11:20",
       },
       App\Models\Post {#4402
         id: 12,
         category_id: null,
         cover: "xxx",
         description: "lorem ipsum",
         slug: "xxx",
         created_at: "2022-01-30 12:11:31",
         updated_at: "2022-01-30 12:11:31",
       },
       App\Models\Post {#4403
         id: 13,
         category_id: null,
         cover: "https://via.placeholder.com/600x400.png/00dd66?t",
         description: "jhghchgchg vhytyhc jhhytytd jhhgcuch",
         slug: "httpsviaplaceholdercom600x400png00dd66t",
         created_at: "2022-01-30 12:24:31",
         updated_at: "2022-01-30 12:24:31",
       },
       App\Models\Post {#4404
         id: 14,
         category_id: null,
         cover: "https://via.placeholder.com/600x40",
         description: "htdyrd jhfhf jhfjgf jhfhtf",
         slug: "httpsviaplaceholdercom600x40",
         created_at: "2022-01-30 12:27:19",
         updated_at: "2022-01-30 12:27:19",
       },
     ],
   }
>>> $post = Post::find(14)
=> App\Models\Post {#4379
     id: 14,
     category_id: null,
     cover: "https://via.placeholder.com/600x40",
     description: "htdyrd jhfhf jhfjgf jhfhtf",
     slug: "httpsviaplaceholdercom600x40",
     created_at: "2022-01-30 12:27:19",
     updated_at: "2022-01-30 12:27:19",
   }
>>> $category = Category::find(5)
=> App\Models\Category {#4404
     id: 5,
     name: "Documentari",
     slug: "documentari",
     created_at: "2022-01-30 12:10:00",
     updated_at: "2022-01-30 12:10:00",
   }
>>> $category->posts;
=> Illuminate\Database\Eloquent\Collection {#4392
     all: [],
   }
>>> $category->posts;
=> Illuminate\Database\Eloquent\Collection {#4392
     all: [],
   }
>>> $category->posts()->save($post);
=> App\Models\Post {#4379
     id: 14,
     category_id: 5,
     cover: "https://via.placeholder.com/600x40",
     description: "htdyrd jhfhf jhfjgf jhfhtf",
     slug: "httpsviaplaceholdercom600x40",
     created_at: "2022-01-30 12:27:19",
     updated_at: "2022-01-30 13:06:57",
   }
>>> 
```