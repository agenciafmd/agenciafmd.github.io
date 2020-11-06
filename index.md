# Backend

Instruções para que o backend funcione como um só

**Importante:**
Os itens marcados com :warning: não seguem a convenção do laravel

## Como nomear

### Controller

Singular acompanhada do sufixo `Controller`

```php
ArticleController.php
LeadController.php
```

### Request

Singular acompanhada do sufixo `Request`

```php
ArticleRequest.php
LeadRequest.php
```

### Listeners

Sobre o que se trata o listener com o sufixo `EventSubscriber`

```php
PageCacheEventSubscriber.php
```

### Rota

:warning: Grande parte das nossas rotas são compostas de `prefixo.controller.método`

- Prefixo `frontend` para o pacote frontend
- Prefixo `admix` para os pacotes do admix
- Prefixo `api.frontend` para os endpoints utilizados no frontend
- Prefixo `api.admix` para os endpoints utilizados no admix
- Utilizar o nome da controller (camelCase)
- Utilizar o nome do método (camelCase)
- Para palavras compostas, utilizar camelCase

***Somente a rota da home, deveremos omitir a `FrontendController::class`.***

```php
Route::get('/', [FrontendController::class, 'index'])
    ->name('frontend.index');
Route::get('/contato', [ContactController::class, 'index'])
    ->name('frontend.contact.index');
Route::get('/faq', [FaqController::class, 'index'])
    ->name('frontend.faq.index');
Route::get('/fale-conosco', [ContactUsController::class, 'index'])
    ->name('frontend.contactUs.index');
Route::post('/fale-conosco', [ContactUsController::class, 'sendMessage'])
    ->name('frontend.contactUs.sendMessage');
```

### Model

Singular

```
Article.php
Lead.php
```

### Relacionamentos

**hasOne / belongsTo**

Singular e com o mesmo nome da model relacionada

```php
public function type()
{
    return $this->belongsTo(Type::class);
}

public function brand()
{
    return $this->belongsTo(Brand::class);
}
```

**Demais relacionamentos** 

Plural e com o mesmo nome da model relacionada 

```php
public function vehicles()
{
    return $this->hasMany(Vehicle::class);
}
```

### Tabelas

A tabela deve ter o nome no plural de sua model.
Procure utilizar somente uma palavra

Model | Tabela 
--- | ---
User | users 
Lead | leads 
ArticleCategory | article_categories 

Quando a tabela for do tipo `pivot`, 
o nome será o nome da model em ordem alfabética

Model | Tabela 
--- | ---
UserPost | post_user 
UserYear | user_year

As colunas da tabela, deverão estar em snake_case.
Dê preferência para uma palavra e singular 

```
is_active
name
description
published_at
```

### Variáveis

Plural para collection e array

```php
$users = collect(['Irineu', 'Carlos', 'Tarsisio', 'Eduardo']);
```

Singular para valores simples

```php
$article = Article::first();
```

camelCase para palavras compostas

```php
$articleCategories = Category::get();
$articleCategory = Category::first();
```

### Blades 

:warning: Os nomes dos arquivos deverão estar slugados, em inglês e com a extensão `.blade.php`

```
about-us.blade.php
```

### Helpers

Os helpers, devem ser escritos em snake_case

```php
if (!function_exists('only_numbers')) {
    function only_numbers($string)
    {
        return preg_replace('/[^0-9]/', '', $string);
    }
}
```
---

## Como se comportar no frontend

### Estrutura

```bash
frontend
├── composer.json
├── ide-blade.json
└── src
    ├── config
    │   └── frontend.php
    ├── Http
    │   ├── Controllers
    │   │   ├── ApiController.php
    │   │   ├── ContactController.php
    │   │   ├── FrontendController.php
    │   │   ├── HtmlController.php
    │   ├── Livewire
    │   │   ├── Contact.php
    │   ├── helpers.php
    ├── Listeners
    │   └── PageCacheEventSubscriber.php
    ├── Providers
    │   ├── EventServiceProvider.php
    │   ├── FrontendServiceProvider.php
    │   ├── LivewireServiceProvider.php
    │   └── RouteServiceProvider.php
    ├── resources
    │   ├── lang
    │   │   ├── en_US.json
    │   │   └── es_ES.json
    │   └── views
    │       ├── components
    │       │   ├── banner-home.blade.php
    │       │   ├── filter-choose.blade.php
    │       │   └── picture.blade.php
    │       ├── html
    │       │   ├── index.blade.php
    │       │   ├── internal.blade.php
    │       │   ├── master.blade.php
    │       │   ├── partials
    │       │   │   ├── footer.blade.php
    │       │   │   ├── header.blade.php
    │       │   │   ├── message.blade.php
    │       │   │   └── picture.blade.php
    │       │   └── tema.blade.php
    │       ├── internal.blade.php
    │       ├── livewire
    │       │   ├── contact.blade.php
    │       ├── master.blade.php
    │       ├── pages
    │       │   ├── about-us.blade.php
    │       │   ├── contact
    │       │   │   └── index.blade.php
    │       │   ├── critical-path-demo.blade.php
    │       │   ├── index.blade.php
    │       │   ├── offline.blade.php
    │       ├── partials
    │       │   ├── footer.blade.php
    │       │   ├── header.blade.php
    │       │   ├── message.blade.php
    │       │   ├── pagination.blade.php
    │       │   └── picture.blade.php
    └── routes
        ├── api.php
        └── web.php
```

#### config/frontend.php

Colocamos algumas configurações que possam ser comuns em algumas partes do pacote.
Imaginando que é preciso aplicar um filtro de ordenação na página de listagem de veículos.
Desmembramos as opções nesse arquivo.

```php
<?php

return [
    'sort' => [
        [
            'label' => 'Destaque',
            'slug' => 'destaque',
            'field' => 'star',
            'sort' => 'asc',
        ],
        [
            'label' => 'Valor (Menor - Maior)',
            'slug' => 'valor',
            'field' => 'value',
            'sort' => 'asc',
        ],
        [
            'label' => 'Valor (Maior - Menor)',
            'slug' => 'valor-desc',
            'field' => 'value',
            'sort' => 'desc',
        ],
    ],       
];
```

**Controller**

```php
// preparamos os filtros que vamos utilizar na view
$view['sort'] = collect(config('frontend.sort'))
    ->mapWithKeys(function ($item) {
        return [
            add_query_params(['sort' => $item['slug']]) => $item['label'],
        ];
    })
    ->toArray();
//...
Vehicle::query()
    ->when($request->sort, function ($query) use ($request) {
        $sort = collect(config('frontend.sort'))
            ->where('slug', $request->sort)
            ->first();

        if (!$sort) {
            return false;
        }

        $query->orderBy($sort['field'], $sort['sort']);
    });
```

**View**

```php
// consumimos os dados preparados pela nossa controller 
{!! Form::select('sort', ['' => '-'] + $sort, add_query_params(['sort' => request()->sort]), [
    'class' => 'custom-select jq-sort',
    'id' => 'ordenar-listagem'
]) !!}
```

#### Http/helpers.php

Aqui colocamos as funçõeszinhas marotas que nos quebra um galhão.

```php
if (!function_exists('remove_query_params')) {
    function remove_query_params(array $params = [])
    {
        $url = url()->current(); // get the base URL - everything to the left of the "?"
        $query = request()->query(); // get the query parameters (what follows the "?")

        foreach ($params as $param) {
            unset($query[$param]); // loop through the array of parameters we wish to remove and unset the parameter from the query array
        }

        return $query ? $url . '?' . http_build_query($query) : $url; // rebuild the URL with the remaining parameters, don't append the "?" if there aren't any query parameters left
    }
}
```

#### Listeners/PageCacheEventSubscriber.php

Aqui cuidamos da limpeza de cache das páginas. 
Escutamos os eventos do eloquent e executamos o page-cache clear

```php
class PageCacheEventSubscriber
{
    public function subscribe($events)
    {
        if (app()->runningInConsole()) {
            return false;
        }

        $events->listen(
            'eloquent.saved: Agenciafmd\Faqs\Faq',
            'Agenciafmd\Frontend\Listeners\PageCacheEventSubscriber@onFaqSaved'
        );
    }

    public function onFaqSaved($model)
    {
        $this->clearUrl(route('frontend.faq.index'));
    }
}
```

#### Http/Livewire

A ideia do livewire, é permitir criar um componente, principalmente o formulário de contato, 
para que seja possivel cachearmos a página inteira.
 
Sempre que for utilizar o exemplo que vem junto com a aplicação, 
lembre-se de remover os itens que não são utilizados (upload por exemplo)

### Boas práticas

#### Route Model Bind

#### Select encadeado

#### Cache

#### Thumb

#### Seed