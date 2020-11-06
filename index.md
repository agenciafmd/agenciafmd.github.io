# Backend

Instruções para que o backend funcione como um só

**Importante:**
Os itens marcados com :warning: não seguem a convenção do laravel

## Como nomear

### Controller

Singular acompanhada do sufixo `Controller`

```
ArticleController
LeadController 
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

Obs. Somente a rota da home, deveremos omitir o metodo index.

```
frontend.index
frontend.contact.index
frontend.faq.index
frontend.contactUs.show
frontend.contactUs.sendMessage
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

```
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