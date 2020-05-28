---
layout: default
title: Boas Práticas
parent: Backend
permalink: /backend/boas-praticas
nav_order: 1
---

# Boas Práticas
{: .no_toc }

## Indice
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Use a nomenclatura do Laravel

Siga a convenção de nomes aceita pela comunidade:

O que | Como | Bom | Ruim
------------ | ------------- | ------------- | -------------
Controller | singular | ArticleController | ~~ArticlesController~~
Rotas | plural | articles/1 | ~~article/1~~
Rotas nomeadas | snake_case com dot notation | users.show_active | ~~users.show-active, show-active-users~~
Model | singular | User | ~~Users~~
Relacionamentos hasOne ou belongsTo | singular | articleComment | ~~articleComments, article_comment~~
Outros relacionamentos | plural | articleComments | ~~articleComment, article_comments~~
Table | plural | article_comments | ~~article_comment, articleComments~~
Pivot table | models no singular em ordem alfabética | article_user | ~~user_article, articles_users~~
Colunas em tabelas | snake_case sem o nome da model | meta_title | ~~MetaTitle; article_meta_title~~
Propriedades da model | snake_case | $model->created_at | ~~$model->createdAt~~
Chaves estrangeiras | nome da model no singular com o sufixo _id | article_id | ~~ArticleId, id_article, articles_id~~
Chaves primárias | - | id | ~~custom_id~~
Migrações | - | 2017_01_01_000000_create_articles_table | ~~2017_01_01_000000_articles~~
Métodos | camelCase | getAll | ~~get_all~~
Métodos em controllers | [table](https://laravel.com/docs/master/controllers#resource-controllers) | store | ~~saveArticle~~
Métodos e classes de teste | camelCase | testGuestCannotSeeArticle | ~~test_guest_cannot_see_article~~
Variáveis | camelCase | $articlesWithAuthor | ~~$articles_with_author~~
Collection | descritivo, plural | $activeUsers = User::active()->get() | ~~$active, $data~~
Object | descritivo, singular | $activeUser = User::active()->first() | ~~$users, $obj~~
Config e arquivos de linguagem | snake_case | articles_enabled | ~~ArticlesEnabled; articles-enabled~~
View | kebab-case | show-filtered.blade.php | ~~showFiltered.blade.php, show_filtered.blade.php~~
Config | snake_case | google_calendar.php | ~~googleCalendar.php, google-calendar.php~~
Contract (interface) | adjetivo ou nome | Authenticatable | ~~AuthenticationInterface, IAuthentication~~
Trait | adjective | Notifiable | ~~NotificationTrait~~

---

## Use sintaxes pequenas e legíveis

Ruim:

```php
$request->session()->get('cart');
$request->input('name');
```

Bom:

```php
session('cart');
$request->name;
```

Mais exemplos:

Sintaxe comum | Pequena e mais legíveis
------------ | -------------
`Session::get('cart')` | `session('cart')`
`$request->session()->get('cart')` | `session('cart')`
`Session::put('cart', $data)` | `session(['cart' => $data])`
`$request->input('name'), Request::get('name')` | `$request->name`
`return Redirect::back()` | `return back()`
`is_null($object->relation) ? $object->relation->id : null }` | `optional($object->relation)->id`
`return view('index')->with('title', $title)->with('client', $client)` | `return view('index', $view)`
`$request->has('value') ? $request->value : 'default';` | `($request->value ?? 'default')`
`Carbon::now(), Carbon::today()` | `now(), today()`
`App::make('Class')` | `app('Class')`
`->where('column', '=', 1)` | `->where('column', 1)`
`->select('id', 'name')->get()` | `->get(['id', 'name'])`

---

## Seja explicito na variável

Ruim:

```php
public function getArticles()
{
    return $this->whereHas('user', function ($q) {
            $q->where('is_active', 1);
        })->get();
}

@foreach($users as $k => $v)
    {% raw %}{{ $v->name }}{% endraw %}
@endforeach
```

Bom:

```php
public function getArticles()
{
    return $this->whereHas('user', function ($query) {
            $query->where('is_active', 1);
        })->get();
}

@foreach($users as $user)
    {% raw %}{{ $user->name }}{% endraw %}
@endforeach
```

---

## Saia o quanto antes

Evite utilizar `else` onde não é necessário

Ruim:

```php
class PageCacheEventSubscriber
{
    public function subscribe($events)
    {
        if (!app()->runningInConsole()) {
            // faz algo
        }
        else {
            // não faz nada
        }       
    }
}
```

Bom:

```php
class PageCacheEventSubscriber
{
    public function subscribe($events)
    {
        if (app()->runningInConsole()) {
            return false;
        }
            
        // faz algo    
    }
}
```

---

## DRY (Don't Repeat Yourself)

Reutilize seu código sempre que possível. 
A ideia da responsabilidade única ajuda você a evitar duplicação. 

Nas **models**, podemos usar o [scope](https://laravel.com/docs/7.x/eloquent#query-scopes)

Nos **blades**, podemos usar os [includes](https://laravel.com/docs/7.x/blade#including-subviews) 

Ruim:

```php
public function getIsActive()
{
    return $this->where('is_active', 1)
        ->whereNotNull('deleted_at')
        ->get();
}

public function getArticles()
{
    return $this->whereHas('user', function ($query) {
            $query->where('is_active', 1)
                ->whereNotNull('deleted_at');
        })->get();
}
```

Bom:

```php
public function scopeIsActive($query)
{
    return $query->where('is_active', 1)
        ->whereNotNull('deleted_at');
}

public function getIsActive()
{
    return $this->isActive()
        ->get();
}

public function getArticles()
{
    return $this->whereHas('user', function ($query) {
            $query->isActive();
        })->get();
}
```

---

## Mass Assignment

Dê preferência para o Mass Assignment

Ruim:

```php
$article = new Article();
$article->title = $request->title;
$article->content = $request->content;
$article->verified = $request->verified;
// Adicionar categoria em artigos
$article->category_id = $category->id;
$article->save();
```

Bom:

```php
$category->article()
    ->create($request->validate());
```

---

## Valide os dados

Sempre insira os dados [validados](https://laravel.com/docs/7.x/validation#quick-writing-the-validation-logic) na model

Ruim:

```php
public function store(Request $request)
{    
    Post::create($request->all());
}
```

Bom:

```php
public function store(PostRequest $request)
{    
    Post::create($request->validate());
}
```

---

## Evite Queries na View

Faça o possível para evitar utilizar queries na view

Ruim:

```php
@foreach (User::all() as $user)
    {% raw %}{{ $user->name }}{% endraw %}
@endforeach
```

Bom:

```php
$view['users'] = User::get();

...

@foreach ($users as $user)
    {% raw %}{{ $user->name }}{% endraw %}
@endforeach
```

---

## Eager Loading e N+1

Utilize o Eager Loading para evitar queries N+1

Ruim:

```php
$view['users'] = User::get();

...

@foreach ($users as $user)
    {% raw %}{{ $user->profile->name }}{% endraw %}
@endforeach
```

Bom:

```php
$view['users'] = User::with('profile')
    ->get();

...

@foreach ($users as $user)
    {% raw %}{{ $user->profile->name }}{% endraw %}
@endforeach
```

---

## Não valide na Controller, utilize o Request

Valide sempre os campos no seu [Request](https://laravel.com/docs/7.x/validation#quick-writing-the-validation-logic)

Ruim:

```php
public function store(Request $request)
{
    $request->validate([
        'title' => 'required|unique:posts|max:255',
        'body' => 'required',
        'publish_at' => 'nullable|date',
    ]);

    ....
}
```

Bom:

```php
public function store(PostRequest $request)
{    
    ....
}

class PostRequest extends Request
{
    public function rules()
    {
        return [
            'title' => [
                'required',
                'unique:posts',
                'max:255',
            ],
            'body' => [
                'required',
            ],
            'publish_at' => [
                'nullable', 
                'date',
            ],
        ];
    }
}
```

---

## Não separe os validadores por pipe, utilize o array

Ruim:

```php
public function store(Request $request)
{
    $request->validate([
        'title' => 'required|unique:posts|max:255',
        'body' => 'required',
        'publish_at' => 'nullable|date',
    ]);

    ....
}
```

Bom:

```php
public function store(PostRequest $request)
{    
    ....
}

class PostRequest extends Request
{
    public function rules()
    {
        return [
            'title' => [
                'required',
                'unique:posts',
                'max:255',
            ],
            'body' => [
                'required',
            ],
            'publish_at' => [
                'nullable', 
                'date',
            ],
        ];
    }
}
```

---

## Não esqueça da bag do formulário

Para o admix, utilize sempre o admix

```php
protected $errorBag = 'admix';
```

Para o front, utilize sempre o nome do formulário

```php
protected $errorBag = 'contacts';
```

---

## Traduza os atributos

```php
public function attributes()
{
    return [
        'title' => 'título',
        'body' => 'texto',
        'publish_at' => 'data de publicação'
    ];
}
```

---

## Traduza as mensagens somente em último caso

Evite traduzir as mensagens por completo.
Faça somente se o texto estiver muito discrepante da realidade. 
Ex. Datas

Ruim:

```php
public function messages()
{
    return [
        'required' => 'Este campo em especial é obrigatório',
        'title.required' => 'O campo titulo é obrigatório',
        'date_format' => 'A data de nascimento é inválida',
    ];
}
```

Bom:

```php
public function messages()
{
    return [
        'dob.date_format' => 'Data de nascimento não confere com o formato 00/00/0000.',
    ];
}
```

---

---

## Não recupere informações diretamente do `.env`

Coloque os dados em arquivos de configuração e recupere através do helper `config()`.

Ruim:

```php
$apiKey = env('API_KEY');
```

Bom:

```php
// config/api.php
'key' => env('API_KEY'),

// Use data
$apiKey = config('api.key');
```

---

## Serviços de terceiros

Para serviços de terceiros, inclua as configurações em `config/services.php`.
Respeita a estrutura do array no nome na variavel

Ruim:

```php
...
'google' => [
    'api_key' => env('GOOGLEAPIKEY'),
    'tagmanager' => env('GOOGLETAGMANAGER'),
    'site_verification' => env('GOOGLESITEVERIFICATION'),
],
...
```

Bom:

```php
'google' => [
    'api_key' => env('GOOGLE_API_KEY'),
    'tagmanager' => env('GOOGLE_TAGMANAGER'),
    'site_verification' => env('GOOGLE_SITE_VERIFICATION'),
],
```

---