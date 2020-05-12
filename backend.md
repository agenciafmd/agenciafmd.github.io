---
title: Backend
description: Boas práticas para o desenvolvimento
rank: 2
---

## Boas práticas para o desenvolvimento

A idéia deste documento, é manter toda a equipe atualizada sobre as boas práticas / funcionalidades aplicadas no desenvolvimento.

- [Laravel](#laravel)
    - [Models Gordas, Controllers Magros](#models-gordas-controllers-magros)

## Laravel 

### Models Gordas, Controllers Magros

Coloque toda a lógica relacionada a banco em modelos Eloquent ou em repositórios caso você esteja usando Query Builder ou consultas SQL.

Ruim:

```php
public function index()
{
    $views['clients'] = Client::verified()
        ->with(['orders' => function ($q) {
            $q->where('created_at', '>', Carbon::today()->subWeek());
        }])
        ->get();

    return view('agenciafmd/frontend::pages.index', $views);
}
```

Bom:

```php
public function index()
{
    $views['clients'] = Client::getWithNewOrders();
    
    return view('agenciafmd/frontend::pages.index', $views);
}

class Client extends Model
{
    public function getWithNewOrders()
    {
        return $this->verified()
            ->with(['orders' => function ($q) {
                $q->where('created_at', '>', Carbon::today()->subWeek());
            }])
            ->get();
    }
}
```

[🔝Voltar](#laravel)

### Fonte

https://github.com/jonaselan/laravel-best-practices