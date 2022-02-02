# Mailables

## create a form in the contacts page

```php
<form action="#" method="post">
    @csrf
    <div class="form-group">
        <label for="name">Name</label>
        <input type="text" name="name" id="name" class="form-control" placeholder="Mario Bross" aria-describedby="nameHelp">
        <small id="nameHelp" class="text-muted">Type your name above</small>
    </div>
    <div class="form-group">
        <label for="email">Email</label>
        <input type="email" name="email" id="email" class="form-control" placeholder="Mario Bross" aria-describedby="emailHelp">
        <small id="emailHelp" class="text-muted">Type your email above</small>
    </div>
    <div class="form-group">
        <label for="body">Message Body</label>
        <textarea class="form-control" name="body" id="body" rows="5"></textarea>
    </div>

    <button type="submit" class="btn btn-primary"><i class="fas fa-envelope-open fa-lg fa-fw"></i> Send</button>
</form>

```

## add a POST route and controller method for the form

nel file web.php

```php
Route::post('contacts', 'PageController@sendForm')->name('contacts.send');

```

Ora modifichiamo l'attributo action del form per fargli inviare la richiesta alla nosta rotta.

```php
<form action="{{route('contacts.send')}}" method="post">
```

Adesso implementiamo il metodo nel controller per gestire la richiesta, nel file PageController.php

```php
public function sendForm(Request $request)
{
    ddd($request->all());

}
```


## [Opzione1] Generate a mailable class with a view

```bash
php artisan make:mail ContactFormMail
```

## Implement the Mailbable's class build() metod

nel metodo build aggiungiamo anche from e subject, from puo essere omesso una volta impostato un indirizzo globale.

```php
    /**
     * Build the message.
     *
     * @return $this
     */
    public function build()
    {
        return $this
            ->from('blog@example.com')
            ->subject("New Contact Message")
            ->view('emails.contactForm');
    }
```

Ora crea la view per il messaggio in views/emails

## Implement the email view

inside the view/emails/contactForm.blade.php

```html

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Email</title>
</head>
<body>

<h4>Contact form message</h4>

</body>
</html>
```

## How to Pass data to the email view

La classe mailable creata prima mette a disposizione della view tutto quello che passiamo al suo constructor.

Nel file ContactFormMail.php

```php
 public $data;

    /**
     * Create a new message instance.
     *
     * @return void
     */
    public function __construct(array $data)
    {
        $this->data = $data;
    }
```

`$data` in questo caso rappresenta la richiesta che passeremo a questa classe quando la useremo per inviare la mail. É praticamente l'array di dati validati che l'utente inserisce quando compila il form.

### Compose the email message

Ora componiamo la view dell'email, nel file emails/contactForm.blade.php

```html

<h4>Hi Admin,</h4>

<p>You received a new message.</p>

<em>From: {{$data['name']}}</em>
<em>Email: {{$data['email']}}</em>

<em>Message</em>
<p>{{$data['body']}}</p>


Thanks,
{{ config('app.name') }}
```

A seconda di chi riceve il messaggio creeremo la nostra mail. In questo caso la inviiamo all'admin del sito.

## Validiamo il form e visualizziamo la mail prima di inviarla

[Documentation](https://laravel.com/docs/7.x/mail#rendering-mailables)

nel file PageController.php

```php

$valData = $request->validate(
    [
    'name' => 'required',
    'email' => 'required|email',
    'body' => 'required'
    ]
);
return (new ContactMail($valData))->render();

```

É passando alla mailable i dati validati che poi possiamo riprenderli nella view e mostrarli all'utente.

## Configuriamo Mailtrap ed inviamo la mail

Dopo aver effettuato la registrazione a mailtrap e modificato il file .env possiamo modificare il metodo nel PageController per inviare la mail.

```php

   public function sendForm(Request $request)
    {
       
        // validate the form data
        $valData = $request->validate(
            [
            'name' => 'required',
            'email' => 'required | email',
            'body' => 'required'
            ]
        );

        // send an email with the data
        Mail::to('admin@fabiopacifici.com')
            ->cc($valData['email'])
            ->send(new ContactMail($valData));
        return redirect()->back()->with('message', 'Message sent successfully');

    }

```

Con il metodo back() reindirizziamo l'utente alla pagina precedente e con with() passiamo un messaggio di conferma tramite la session alla view del contact form.
Qualora ci fossero errori dobbiamo anche mostrarli all'utente.

## Mostriamo errori di validazione e messaggio session

Nel contact form ora aggiungiamo il messaggio tramite la session se l'invio é andato a buon fine

```php

@if(session('message'))
    <div class="alert alert-success" role="alert">
        <strong>{{session('message')}}</strong>
    </div>
@endif

```

Messaggi di errore se la validazione fallisce.

```php
@if ($errors->any())
<div class="alert alert-danger">
    <ul>
        @foreach ($errors->all() as $error)
        <li>{{ $error }}</li>
        @endforeach
    </ul>
</div>
@endif

```

## [Opzione2] Generate a Mailable class with markdown

Possiamo anche lasciar fare a laravel la gran parte del lavoro generando una mailable con view stile markdown (tipo questa guida)

```bash
php artisan make:mail --markdown=emails.contactEmail ContactFormMailable
```

Artisan genera per noi una view con questo contenuto che possiamo modificare passandogli dati come fatto prima.

```md

@component('mail::message')
# Introduction

The body of your message.

@component('mail::button', ['url' => ''])
Button Text
@endcomponent

Thanks,<br>
{{ config('app.name') }}
@endcomponent
```

## Salvare i messaggi ricevuti nel database

Un altro passaggio sarebbe quello di salvare nel db tutti i messaggi ricevuti tramite il sito. Facciamolo!

## Crea Modello e controller dedicato

Stavolta creeremo anche un controller dedicato allo scopo oltre ad un modello e una migrazione

```bash

php artisan make:model -cm Contact
```

## Puntiamo la rotta al nuovo controller

```php
Route::post('contacts', 'ContactController@store')->name('contacts');

```

## Dump the form request inside the ContactController

```php
 public function store(Request $request)
    {
        //
        dd($request->all());
    }
```

## Add columns to the migation

```php
Schema::create('contact', function (Blueprint $table) {
    $table->id();
    $table->string('name');
    $table->string('email');
    $table->text('message');
    $table->timestamps();
});

```

## Migrate the db

```bash
php artisan migrate
```

## Create a mailable class with markdown view

```bash
php artisan make:mail -m emails.contacts ContactFormMail
```

## Update the build method

```php
 public function build()
    {
        return $this->from('noreply@example.com')->markdown('mail.contacts');
    }
```

## Try to send an email from the ContactController store method

```php
use App\Mail\ContactFormMail;
use Illuminate\Support\Facades\Mail;
```

before dd the request in the ContactController.php

```php
public function store(Request $request)
    {
        //
        //dd($request->all());
        $to = 'admin@fabiopacifici.com';
        Mail::to($to)->send(new ContactFormMail);
        
    }

```

## Pass data to the mailable via its constructor

```php

/** 
     * The contact instance
     * @var \App\Contact
     */
    protected $contact;

    /**
     * Create a new message instance.
     *
     * @return void
     */
    public function __construct(Contact $contact)
    {
        $this->contact = $contact;
    }
```

## Pass the object when the mailable class is instanciated

in the store method of the ContactController

```php
 public function store(Request $request)
    {
        //
        //dd($request->all());
        
        $mail = new Contact();
        $mail->name = $request->name;
        $mail->email = $request->email;
        $mail->message = $request->message;
        $mail->save();
        $to = 'admin@example.com';
        Mail::to($to)->send(new ContactFormMail($mail));
        
    }
```

## Pass the data to the markdown view

edit the build method

```php

 public function build()
    {
        return $this->from('noreply@example.com')->markdown('mail.contacts')->with([
            'name' => $this->contact->name,
            'message' => $this->contact->message,
            'email' => $this->contact->email,


        ]);
    }
```

edit the mailable template

```php
@component('mail::message')
# Introduction

The body of your message.
Message: {{ $message }}
From: {{ $name }}
Email: {{ $email }}

@component('mail::button', ['url' => ''])
Button Text
@endcomponent

Thanks,<br>
{{ config('app.name') }}
@endcomponent

```

## Validate the contact form

contact controller

```php

public function store(Request $request)
    {
        //dd($request->all());
        $data = $request->validate([
            'name' => 'required',
            'email' => 'required',
            'message'=> 'required'
        ]);
 
        $mail = Contact::create($data);
        $to = 'admin@example.com';
        Mail::to($to)->send(new ContactFormMail($mail));
        
    }
```

Contact model add the fillable props

```php
    protected $fillable = ['name', 'email', 'message'];
```

## Redirect back to the contact form with a message

inside the controller store method

```php

 return redirect()->route('contacts')->with('message', 'Thanks for your message we will reply asap');
```

show the message in the guests.contacts template

```html

@if (session('message'))
<div class="alert alert-success" role="alert">
    <strong>{{ session('message') }}</strong>
</div>
@endif
```

DONE!
