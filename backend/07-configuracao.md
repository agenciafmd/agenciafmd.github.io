---
layout: default
title: Configuração
parent: Backend
permalink: /backend/configuracao
nav_order: 7
---

# Configuração
{: .no_toc }

## Indice
{: .no_toc .text-delta }

1. TOC
{:toc}

---

### Não recupere informações diretamente do `.env`

Coloque os dados em arquivos de configuração e recupere através do helper `config()`.

Ruim:

```php
$apiKey = env('API_KEY');
```

Bom:

```php
// config/api.php
'key' => env('API_KEY'),

// Use data
$apiKey = config('api.key');
```

---

### Serviços de terceiros

Para serviços de terceiros, inclua as configurações em `config/services.php`.
Respeita a estrutura do array no nome na variavel

Ruim:

```php
...
'google' => [
    'api_key' => env('GOOGLEAPIKEY'),
    'tagmanager' => env('GOOGLETAGMANAGER'),
    'site_verification' => env('GOOGLESITEVERIFICATION'),
],
...
```

Bom:

```php
'google' => [
    'api_key' => env('GOOGLE_API_KEY'),
    'tagmanager' => env('GOOGLE_TAGMANAGER'),
    'site_verification' => env('GOOGLE_SITE_VERIFICATION'),
],
```