---
layout: default
title: Convenção
parent: Backend
permalink: /backend/convencao
nav_order: 1
---

# Convenção
{: .no_toc }

## Indice
{: .no_toc .text-delta }

1. TOC
{:toc}

---

### Use a nomenclatura do Laravel

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

### Use sintaxes pequenas e legíveis

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
