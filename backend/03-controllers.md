---
layout: default
title: Controllers
parent: Backend
permalink: /backend/controllers
nav_order: 3
---

# Controllers
{: .no_toc }

## Indice
{: .no_toc .text-delta }

1. TOC
{:toc}

---

### Mass Assignment

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

### Valide os dados

Sempre insira os dados validados na model

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

