---
title: Convenções
parent: APIs
nav_order: 2
---

# Conveções
{: .no_toc }

## Conteúdo
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Verbos HTTP

O contexto de uma API RESTful é baseado em recursos e ações. Os verbos HTTP (método) são usados para definir as ações que serão realizadas nos recursos.

É importante destacar que os recursos devem estar sempre no **plural**.

Abaixo estão os verbos HTTP mais comuns e suas ações:

<div class="code-example" markdown="1">

| Endpoint       | Método   | Ação                                                         |
|:---------------|:---------|:-------------------------------------------------------------|
| /products      | GET      | Retorna a lista de produtos                                  |
| /products      | POST     | Insere um novo produto                                       |
| /products/{id} | GET      | Retorna o produto do id = {id}                               |
| /products/{id} | PUT      | Altera os dados do produto id = {id}                         |
| /products/{id} | PATCH    | Altera algumas informações específicias do produto id = {id} |
| /products/{id} | DELETE   | Remove o produto com o id = {id}                             |

</div>


## Respostas HTTP

### Códigos de Retorno

<div class="code-example" markdown="1">

| Código | Descrição             |
|:-------|:----------------------|
| 200    | OK                    |
| 201    | Created               |
| 204    | No Content            |
| 400    | Bad Request           |
| 404    | Not Found             |
| 500    | Internal Server Error |

</div>

### Padrão de códigos de retorno por verbo HTTP

<div class="code-example" markdown="1">

| Método   | Código Retorno Sucesso   | Código Retorno Erro |
|:---------|:-------------------------|:--------------------|
| GET      | 200                      | 400 ou 404          |
| POST     | 201                      | 400                 |
| PUT      | 200 ou 204               | 400 ou 404          |
| DELETE   | 200 ou 204               | 404                 |

</div>

## Formato de Respostas

É importante definir um formato de resposta padrão que será retornado por todos os endpoints da API. O formato mais comum é o JSON.

```json
{
  "success": true,
  "message": "Product found",
  "data": {
    "id": 1,
    "name": "Product 1",
    "price": 100.00
  }
}
``` 

## Nomeando variáveis
### Private Fields
Para variáveis privadas usamos o padrão camelCase.

```csharp
private string productName;
```

### Public Fields
Para variáveis públicas usamos o padrão PascalCase.

```csharp
public string ProductName;
```

### Constantes
Para constantes usamos o padrão UPPERCASE.

```csharp
public const string PRODUCT_NAME = "Product 1";
```

## Nomenclatura de Métodos
Os métodos devem ser nomeados de acordo com a ação que eles realizam. O nome do método deve ser um verbo ou uma frase que descreva a ação que ele executa.
Sempre utilizando o padrão PascalCase.

```csharp
public void CreateProduct()
{
    // Código para criar um produto
}   
```