---
layout: default
title: Requests
parent: Backend
nav_order: 1
---

# Requests
{: .no_toc }

## Indice
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Utilize as classes de Request ao invés do validade na Controller

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

## Não separe os validadores por pipe, dê preferência para o array

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
