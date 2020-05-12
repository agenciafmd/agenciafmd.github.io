---
layout: default
title: Views
parent: Backend
permalink: /backend/views
nav_order: 04
---

# Views
{: .no_toc }

## Indice
{: .no_toc .text-delta }

1. TOC
{:toc}

---

### Nada de Queries

Ruim:

```php
@foreach (User::all() as $user)
    {% raw %}{{ $user->name }}{% endraw %}
@endforeach
```

Bom:

```php
$view['users'] = User::get();

...

@foreach ($users as $user)
    {% raw %}{{ $user->name }}{% endraw %}
@endforeach
```

---

### Eager Loading e N+1

Utilize o Eager Loading para evitar queries N+1

Ruim:

```php
$view['users'] = User::get();

...

@foreach ($users as $user)
    {% raw %}{{ $user->profile->name }}{% endraw %}
@endforeach
```

Bom:

```php
$view['users'] = User::with('profile')
    ->get();

...

@foreach ($users as $user)
    {% raw %}{{ $user->profile->name }}{% endraw %}
@endforeach
```