---
title: Guidelines
parent: Backend
---

# Guidelines
{: .no_toc }

## Conteúdo
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Por quê?

Como o Laravel fornece muitas *features* implícitas e/ou convencionadas, temos como objetivo, dar continuidade nesse
fluxo.

Se existir algum jeito documentado de se chegar em algum lugar, faça deste jeito.

Caso monte algo diferente do que temos documentado, seja claro na sua justificativa do motivo de não seguir o padrão.

## Regras Gerais

O estilo deve seguir o [PSR-1](https://www.php-fig.org/psr/psr-1/), [PSR-2](https://www.php-fig.org/psr/psr-2/) e [PSR-12](https://www.php-fig.org/psr/psr-12/).

## Nomes

**NÃO** utilize abreviações em nomes de variáveis, métodos, classes, etc.

Utilize:
- **Inglês** para variáveis, métodos, classes, tabelas, colunas, etc.
- **camelCase** para variáveis, métodos e rotas.
- **snake_case** para tabelas e colunas.
- **PascalCase** para classes.
- **kebab-case** para arquivos, diretórios e urls.
- **UPPERCASE** para constantes.
- **Singular** para controllers.

## Rotas

Seja consistente nos nomes faça o vínculo com a url e a Controller

Utilize:
- **Inglês** para urls privadas.
- **Português** para as urls publicas.
- **Plural** para as urls e nome das rotas. 
- **camelCase** para o nome das rotas e parâmetros.

```php
https://fmd.ag/sobre-nos
https://fmd.ag/dashboard/profile
```

> Não pluralize palavras que não devem ser pluralizadas. (Ex: blog -> blogs, faq -> faqs, etc.)

{: .new-title }
> Faça

```php
use Agenciafmd\Frontend\Http\Controllers\ArticleController;

Route::get('/artigos', [ArticleController::class, 'index'])
    ->name('frontend.articles.index');
Route::get('/artigos/{frontArticle}', [ArticleController::class, 'show'])
    ->name('frontend.articles.show');
```

{: .new-title }
> Faça

```php
use Agenciafmd\Frontend\Http\Controllers\Dashboard\IndexController;
use Agenciafmd\Frontend\Http\Controllers\Dashboard\ProfileController;
use Agenciafmd\Frontend\Http\Controllers\Dashboard\ArticleController;

Route::get('/dashboard', [IndexController::class, 'index'])
    ->name('frontend.dashboard.index');
Route::get('/dashboard/profile', [ProfileController::class, 'index'])
    ->name('frontend.dashboard.profile.index');
Route::put('/dashboard/profile', [ProfileController::class, 'update'])
    ->name('frontend.dashboard.profile.update');
Route::get('/dashboard/articles', [DashboardArticleController::class, 'index'])
    ->name('frontend.dashboard.articles.index');
Route::post('/dashboard/articles', [DashboardArticleController::class, 'store'])
    ->name('frontend.dashboard.articles.store');
```

Rotas do Livewire.

> Note que o my-account foi omitido para não deixar a url /profile órfã

{: .new-title }
> Faça

```php
use Agenciafmd\Frontend\Livewire\Pages;

Route::get('/dashboard', Pages\Dashboard::class)
->name('frontend.pages.dashboard');
Route::get('/dashboard/profile', Pages\Dashboard\Profile\MyAccount::class)
->name('frontend.pages.dashboard.profile.myAccount');
Route::get('/dashboard/profile/change-password', Pages\Dashboard\Profile\ChangePassword::class)
->name('frontend.pages.dashboard.profile.changePassword');
```

Linke o caminho da rota com a controller e o método.

{: .new-title }
> Faça

```php
Route::get('customers/me', [CustomerController::class, 'me'])
    ->name('customer.me');
```

{: .warning-title }
> Não faça

```php
Route::get('customers/me', 'CustomerController@me')
    ->name('customer.me');
```

Utilize o agrupamento apenas para middlewares.

{: .new-title }
> Faça

```php
Route::middleware('auth:api')
    ->group(function () {
        Route::get('customers/me', [CustomerController::class, 'me'])
            ->name('customer.me');
        Route::put('customers/me', [CustomerController::class, 'update'])
            ->name('customer.update');
        Route::delete('customers/me', [CustomerController::class, 'destroy'])
            ->name('customers.destroy');
});
```

{: .warning-title }
> Não faça

```php
Route::prefix('customers')
    ->name('customers.')
    ->group(function () {
       Route::get('me', [CustomerController::class, 'me'])
           ->middleware('auth:api')
           ->name('me');
       Route::put('me', [CustomerController::class, 'update'])
           ->middleware('auth:api')
           ->name('update');
       Route::delete('me', [CustomerController::class, 'destroy'])
           ->middleware('auth:api')
           ->name('destroy');
    });
```

Deixe os verbos no inicio e o nome da rota por último.

{: .new-title }
> Faça

```php
Route::get('/', [HomeController::class, 'index'])->name('home');
Route::get('open-source', [OpenSourceController::class, 'index'])->name('openSource');
```

{: .warning-title }
> Não faça

```php
Route::name('home')->get('/', [HomeController::class, 'index']);
Route::name('openSource')->get([OpenSourceController::class, 'index']);
```

Os parâmetros devem estar em camelCase e no singular.

{: .new-title }
> Faça

```php
Route::get('articles/{frontArticle}', [ArticleController::class, 'show']);
```

{: .warning-title }
> Não faça

```php
Route::get('articles/{articles}', [ArticleController::class, 'show']);
```

A route url should not start with / unless the url would be an empty string.
As rotas não devem começar com / a menos que a url seja uma string vazia.

{: .new-title }
> Faça

```php
Route::get('/', [HomeController::class, 'index']);
Route::get('open-source', [OpenSourceController::class, 'index']);
```

{: .warning-title }
> Não faça

```php
Route::get('', [HomeController::class, 'index']);
Route::get('/open-source', [OpenSourceController::class, 'index']);
```

## Rota de API

Utilize:
- **Inglês** para todas as urls.
- **Plural** para os resources
- **kebab-case** para os resources.

Evite muitos níveis de _nesting_. Rotas muito profundas são mais difíceis de gerenciar. Limitando o _nesting_, você mantém a simplicidade e melhora a legibilidade.

{: .new-title }
> Faça

```php
/customers/1
/customers/1/orders
```

{: .warning-title }
> Não faça

```php
/projects/1/errors/1/error-occurrences/1
```

## Nullable e União de Tipos

Sempre que possível, utilizar a *short notation* para o *nullable* ao invés de união de tipos.

{: .new-title }
> Faça

```php
public ?string $variable;
```

{: .warning-title }
> Não faça

```php
public string|null $variable;
```

## Tipagem

Seja consistente com a tipagem dos métodos, variáveis e parâmetros.

{: .new-title }
> Faça

```php
public string $string;

public ?string $nullableString;

public array $array;
```

{: .new-title }
> Faça

```php
use Illuminate\Database\Eloquent\Builder

public function scopeIsActive(Builder $builder): void
{
    $builder->where('is_active', 1);
}
```

{: .warning-title }
> Não faça

```php
public $string;

public array|Collection $arrayOrCollection;
```

{: .warning-title }
> Não faça

```php
public function scopeIsActive($builder)
{
    $builder->where('is_active', 1);
}
```

## Use constructor property promotion

Use o `constructor property promotion` se todas as propriedades puderem ser promovidas. 

Para torná-lo legível, coloque cada um em uma linha própria. 

Use uma vírgula após a última propriedade.

{: .new-title }
> Faça

```php
class MyClass {
    public function __construct(
        protected string $firstArgument,
        protected string $secondArgument,
    ) {}
}
```

{: .warning-title }
> Não faça

```php
class MyClass {
    protected string $secondArgument

    public function __construct(protected string $firstArgument, string $secondArgument)
    {
        $this->secondArgument = $secondArgument;
    }
}
```

## Traits
Use as traits inline, em vez de em várias linhas e ordene-as alfabeticamente.

{: .new-title }
> Faça

```php
class MyClass
{
    use TraitA, TraitB;
}
```

{: .warning-title }
> Não faça

```php
class MyClass
{
    use TraitA;
    use TraitB;
}
```

## Strings

Prefira a interpolação de strings ao invés do operador ´.´.

{: .new-title }
> Faça

```php
$greeting = "Hi, I am {$name}.";
```

{: .warning-title }
> Não faça

```php
$greeting = 'Hi, I am ' . $name . '.';
```

## _Happy path_

O ideal é que todas as condições que possam causar um erro sejam verificadas primeiro, e o restante do código seja executado se tudo estiver correto. Isso permite que o _happy path_ fique em uma parte não recuada da função, o que o torna mais legível.

{: .new-title }
> Faça

```php
if (! $goodCondition) {
    throw new Exception;
}
```

{: .warning-title }
> Não faça

```php
if ($goodCondition) {
    // do work
}

throw new Exception;
```

## Evite o uso do _else_

Em geral, _else_ deve ser evitado porque torna o código menos legível. Geralmente, pode ser refatorado usando _early returns_. Isso também fará com que o _happy path_ seja executado por último, o que é o esperado.

{: .new-title }
> Faça

```php
if (! $conditionA) {
    // condition A failed
    
    return;
}

if (! $conditionB) {
    // condition A passed, B failed
    
    return;
}

// condition A and B passed
```

{: .warning-title }
> Não faça

```php
if ($conditionA) {
   if ($conditionB) {
      // condition A and B passed
   }
   else {
     // condition A passed, B failed
   }
}
else {
   // condition A failed
}
```

## Compound ifs

Em geral, _ifs_ separados devem ser prioridade em uma condição composta. Isso torna o _debug_ do código mais fácil.

{: .new-title }
> Faça

```php
if (! $conditionA) {
   return;
}

if (! $conditionB) {
   return;
}

if (! $conditionC) {
   return;
}

// do stuff
```

{: .warning-title }
> Não faça

```php
if ($conditionA && $conditionB && $conditionC) {
  // do stuff
}
```

## Comentários

Comentários devem ser evitados o máximo possível escrevendo um código explicativo. Se precisar usar um comentário, formate-o assim:

```php
// There should be a space before a single line comment.

/*
 * If you need to explain a lot you can use a comment block. Notice the
 * single * on the first line. Comment blocks don't need to be three
 * lines long or three characters shorter than the previous line.
 */
```

Uma possível estratégia para refatorar um comentário é criar uma função com um nome que descreva o comentário.

{: .new-title }
> Faça

```php
$this->calculateLoans();
```

{: .warning-title }
> Não faça

```php
// Start calculating loans
$this->calculate();
```

## Espaço em branco
Declarações devem ser separadas por uma linha em branco. Em geral, sempre adicione linhas em branco entre as declarações, a menos que sejam uma sequência de operações equivalentes de uma linha.

{: .new-title }
> Faça

```php
public function getPage($url)
{
    $page = $this->pages()->where('slug', $url)->first();

    if (! $page) {
        return null;
    }

    if ($page['private'] && ! Auth::check()) {
        return null;
    }

    return $page;
}
```

{: .warning-title }
> Não faça

```php
public function getPage($url)
{
    $page = $this->pages()->where('slug', $url)->first();
    if (! $page) {
        return null;
    }
    if ($page['private'] && ! Auth::check()) {
        return null;
    }
    return $page;
}
```

Não adicione espaços em branco entre os `{}` colchetes.

{: .new-title }
> Faça

```php
if ($foo) {
    $this->foo = $foo;
}
```

{: .warning-title }
> Não faça

```php
if ($foo) {

    $this->foo = $foo;
}
```

## Configurações

Os arquivos de config devem estar em kebab-case.

```php
config/pdf-generator.php
```

As variaveis de config devem estar em snake_case.

```php
// config/pdf-generator.php
return [
    'chrome_path' => env('CHROME_PATH'),
];
```

{: .warning-title }
> Atenção
>
> Não utilize o env() fora dos arquivos de config.

## Artisan

Os comandos devem ser todos em kebab-case. Utilize o formato nome-do-pacote:acao-do-comando.

{: .new-title }
> Faça

```bash
php artisan admix:create-user
```

{: .warning-title }
> Não faça

```bash
php artisan createUserToAdmix
```

O comando deve sempre dar algum feedback sobre qual é o resultado.

```php
// no comando
public function handle()
{
    // faça uma mágica

    $this->info('Mágica feita com sucesso!');
}
```

Quando estamos processando vários itens, importando um csv, por exemplo, é interessante adicionar uma saída dentro do loop, para que o progresso possa ser rastreado. 

No final do comando, forneça um resumo de quanto processamento foi feito.

```php
// no comando
public function handle()
{
    $this->comment("Lendo o csv...");

    // fazendo a mágica
    $items->each(function(Item $item) {
        $this->info("Importando o item de id: {$item-id}...");

        $this->processItem($item);
    });

    $this->comment("{$item->count()} itens importados.");
}
```
## Controllers

As **controllers** que controlam uma **model**, devemos usar o nome do recurso no **singular**.

```php
class PostController
{
    // ...
}
```

Tente manter os **controllers** simples e use as palavras-chave **CRUD** padrão (`index`, `create`, `store`, `show`, `edit`, `update`, `destroy`). 
Extraia para uma nova **controller** se precisar de outras ações.
No exemplo a seguir, poderíamos ter `PostsController@favorite` e `PostsController@unfavorite`, ou poderíamos extrair para uma nova `FavoritePostController`.

```php
class PostController
{
    public function create()
    {
        // ...
    }

    // ...

    public function favorite(Post $post)
    {
        request()->user()->favorites()->attach($post);

        return response(null, 200);
    }

    public function unfavorite(Post $post)
    {
        request()->user()->favorites()->detach($post);

        return response(null, 200);
    }
}
```

Aqui temos um `fallback` para o padrão **CRUD**, usando o `store` e `destroy`  

```php
class FavoritePostController
{
    public function store(Post $post)
    {
        request()->user()->favorites()->attach($post);

        return response(null, 200);
    }

    public function destroy(Post $post)
    {
        request()->user()->favorites()->detach($post);

        return response(null, 200);
    }
}
```

Está é uma sugestão, não precisa seguir a risca, mas é recomendado.

## Views

As views devem ser escritas em `kebab-case`.

```php
//resources/views/open-source.blade.php
class OpenSourceController
{
    public function index() {
        return view('open-source');
    }
}
```

## Validação
Quando usar várias regras para um campo em uma solicitação de formulário, evite usar |, sempre use `array notation`. 
Isso facilitará a aplicação de classes de regras personalizadas a um campo.

{: .new-title }
> Faça

```php
public function rules()
{
    return [
        'email' => [
            'required', 
            'email:rfc,dns',
         ],
    ];
}
```

{: .warning-title }
> Não faça

```php
public function rules()
{
    return [
        'email' => 'required|email',
    ];
}
```

Todas as validações personalizadas devem estar em `snake_case`:

```php
Validator::extend('organisation_type', function ($attribute, $value) {
    return OrganisationType::isValid($value);
});
```

## Blade Templates

Faça a indentação em 4 espaços.

```php
<h1>
    Open Source
</h1>
```

Use as rotas nomeadas.

{% raw %}
```php
<a href="{{ route('frontend.open-source') }}">
    Open Source
</a>
```
{% endraw %}

Não adicione espaços após as diretivas.

{: .new-title }
> Faça

```php
<div>
   @if($condition)
      Something
   @endif
</div>
```

{: .warning-title }
>Não faça

```php
<div>

   @if($condition)
   
      Something
   @endif

</div>
```

## Authorization

Policies devem ser `camelCase`.

{% raw %}
```php
Gate::define('editPost', function ($user, $post) {
    return $user->id == $post->user_id;
});

@can('editPost', $post)
    <a href="{{ route('posts.edit', $post) }}">
        Edit
    </a>
@endcan
```
{% endraw %}

Tente nomear as habilidades usando palavras **CRUD** padrão. Uma exceção: substitua `show` por `view`.

## Traduções

As traduções devem ser renderizadas com a função `__`.

{% raw %}
```php
<h2>{{ __('newsletter.form.title') }}</h2>

{!! __('newsletter.form.description') !!}
```
{% endraw %}

## Nomeando as classes

Nomear as coisas é visto como uma das coisas mais difíceis na programação. É por isso que estabelecemos algumas diretrizes de alto nível para nomear classes.

### Controllers

Geralmente, os controllers são nomeados pela forma **singular** de seu recurso correspondente e um sufixo **Controller**.

ex. `UserController` or `EventDayController`

Quando houver controller não relacionadas a model, você pode se deparar com controllers invocáveis que executam uma única ação. Estes podem ser nomeados pela ação que executam novamente sufixados por **Controller**.

ex. `PerformCleanupController`

### Resources e Transformers

Tanto o **Eloquent Resources** quanto o **Fractal Transformers** devem estar no singular e sufixados com **Resource** / **Collection** ou **Transformer** respectivamente. 
Pense sempre como sendo a **Model** o nosso guia.

```bash
src/Http/Resources/CompanyCollection.php
src/Http/Resources/CompanyResource.php
```

```bash
src/Transformers/AppointmentTransformer.php
src/Transformers/AssociateTransformer.php
```

### Jobs

O nome do Job deve descrever sua ação.

```bash
src/Jobs/UpdateAssociateSubscriptions.php
```

### Events

Eventos são frequentemente disparados antes ou depois do evento real. Isso deve ser muito claro pela tensão usada em seu nome.

ex. `ApprovingLoan` antes da ação `ApproveLoan` ser feita e `LoanApproved` depois que a ação `ApproveLoan` seja completada.

### Listeners

Listeners devem executar uma ação com base em um evento de entrada. Seu nome deve refletir essa ação com um sufixo **Listener**. Isso pode parecer estranho no início, mas evitará colisões de nomes com jobs.

```bash
src/Listeners/UpdateAssociateSubscriptionsListener.php
```

### Commands

Para evitar colisões de nomes, vamos usar o sufixo **Command**.

```bash
src/Commands/UpdateAssociateSubscriptionsCommand.php
```

### Mailables

Novamente, para evitar colisões de nomes, vamos usar o sufixo **Mail**.

```bash
src/Mails/SendMail.php
```

### Enums
Enums não precisam de prefixos, pois geralmente, é claro lendo o nome que é um **Enum**.

ex. OrderStatus or BookingType or Suit