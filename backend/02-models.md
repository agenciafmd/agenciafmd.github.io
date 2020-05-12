---
layout: default
title: Models
parent: Backend
permalink: /backend/models
nav_order: 2
---

# Models
{: .no_toc }

## Indice
{: .no_toc .text-delta }

1. TOC
{:toc}

---

### Models Gordas, Controllers Magros

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

---

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