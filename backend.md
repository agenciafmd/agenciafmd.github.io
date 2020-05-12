---
title: Backend
description: Boas práticas para o desenvolvimento
rank: 2
---

## Boas práticas para o desenvolvimento

A idéia deste documento, é manter toda a equipe atualizada sobre as boas práticas / funcionalidades aplicadas no desenvolvimento.

- [Laravel](#laravel)
    - [Siga a convenção de nomes usada no Laravel](#siga-a-convenção-de-nomes-usada-no-laravel)
    - [Models Gordas, Controllers Magros](#models-gordas-controllers-magros)
    - [Validação](#validação)
    - [Mova a logica de negocios para o Service](#mova-a-logica-de-negocios-para-o-service)
    - [DRY (Don't Repeat Yourself)](#dry-dont-repeat-yourself)
    - [Mass Assignment](#mass-assignment)
    - [Queries na view](#queries-na-view)
    - [Eager Loading e N+1](#eager-loading-e-n1)
    - [Tente sempre usar sintaxes pequenas e legíveis](#tente-sempre-usar-sintaxes-pequenas-e-legveis)
    - [Não recupere informações diretamente do `.env`](#nao-recupere-informações-diretamente-do-env)

## Laravel 

### Siga a convenção de nomes usada no Laravel [🔗](#siga-a-convenção-de-nomes-usada-no-laravel)

Siga também a convenção de nomes aceita pela comunidade Laravel:

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

[Voltar](#laravel)

### Models Gordas, Controllers Magros <a name="models-gordas-controllers-magros"></a>

Mova a lógica do banco para a model.

Ruim:

```php
public function index()
{
    $view['clients'] = Client::isActive()
        ->with(['orders' => function ($query) {
            $query->where('created_at', '>', Carbon::today()->subWeek());
        }])
        ->get();

    return view('agenciafmd/frontend::pages.index', $view);
}
```

Bom:

```php
public function index()
{
    $view['clients'] = Client::getWithNewOrders();
    
    return view('agenciafmd/frontend::pages.index', $view);
}

class Client extends Model
{
    public function getWithNewOrders()
    {
        return $this->isActive()
            ->with(['orders' => function ($query) {
                $query->where('created_at', '>', Carbon::today()->subWeek());
            }])
            ->get();
    }
}
```

[Voltar](#laravel)

### Validação <a name="validação"></a>

- Utilize as classes de Request ao invés do validade na Controller
- Não separe os validadores por pipe, dê preferência para o array

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

[Voltar](#laravel)

### Mova a logica de negocios para o Service

Controller devem ter somente uma responsabilidade, então mova lógica de negócio para outros serviços.

**TODO: trocar exemplo**

Ruim:

```php
public function store(Request $request)
{
    if ($request->hasFile('image')) {
        $request->file('image')->move(public_path('images') . 'temp');
    }

    ....
}
```

Bom:

```php
public function store(Request $request)
{
    $this->articleService->handleUploadedImage($request->file('image'));

    ....
}

class ArticleService
{
    public function handleUploadedImage($image)
    {
        if (!is_null($image)) {
            $image->move(public_path('images') . 'temp');
        }
    }
}
```

[Voltar](#laravel)

### DRY (Don't Repeat Yourself)

Reutilize seu código sempre que possível. A ideia da responsabilidade única ajuda você a evitar duplicação. Isso serve também para templates Blade.

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

[Voltar](#laravel)

### Mass Assignment

Dê preferencia para o Mass Assignment

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

[Voltar](#laravel)

### Queries na view

Evite utilizar queries na view

Ruim:

```php
@foreach (User::all() as $user)
    {{ $user->name }}
@endforeach
```

Bom:

```php
$view['users'] = User::get();

...

@foreach ($users as $user)
    {{ $user->name }}
@endforeach
```

[Voltar](#laravel)

### Eager Loading e N+1

Utilize o Eager Loading para evitar queries N+1

Ruim:

```php
$view['users'] = User::get();

...

@foreach ($users as $user)
    {{ $user->profile->name }}
@endforeach
```

Bom:

```php
$view['users'] = User::with('profile')
    ->get();

...

@foreach ($users as $user)
    {{ $user->profile->name }}
@endforeach
```

[Voltar](#laravel)

### Tente sempre usar sintaxes pequenas e legíveis

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
`$request->has('value') ? $request->value : 'default';` | `$request->get('value', 'default')`
`Carbon::now(), Carbon::today()` | `now(), today()`
`App::make('Class')` | `app('Class')`
`->where('column', '=', 1)` | `->where('column', 1)`
`->select('id', 'name')->get()` | `->get(['id', 'name'])`

[Voltar](#laravel)

### Não recupere informações diretamente do `.env`

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

[Voltar](#laravel)


### Fonte

https://github.com/jonaselan/laravel-best-practices