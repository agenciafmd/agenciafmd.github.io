---
layout: default
title: Validação
parent: Backend
permalink: /backend/validacao
nav_order: 6
---

# Validação
{: .no_toc }

## Indice
{: .no_toc .text-delta }

1. TOC
{:toc}

---

### Não valide na Controller, utilize o Request

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

### Não separe os validadores por pipe, utilize o array

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

### Não esqueça da bag do formulário

Para o admix, utilize sempre o admix

```php
protected $errorBag = 'admix';
```

Para o front, utilize sempre o nome do formulário

```php
protected $errorBag = 'contacts';
```

---

### Traduza os atributos

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
        'dob.date_format' => 'Data de nascimento não confere com o formato Y-m-d.',
    ];
}
```

---
