---
title: Boas Práticas
parent: APIs
nav_order: 3
---

# Boas práticas
{: .no_toc }

## Conteúdo
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Funções
**As funções devem fazer uma coisa. Devem fazê-la bem. Devem fazer apenas isso.**

{: .new-title }
> Faça
```text
A quantidade ideal de parâmetros para uma função é zero. Em seguida, um (mônade) e,
em seguida, dois (díade). 
```

{: .warning-title }
> Não faça
```text
Três (tríade) ou mais devem ser evitados sempre que possível. Mais de três (políade) requerem
justificativa e devem ser evitados com rigor.
```

## Comentários

{: .warning-title }
> Não faça
```text
/* 
Comentários, geralmente, são suportes ou desculpas para códigos ruins ou justificativas para
a falta de decisões, amontoados como se o programador estivesse falando com si mesmo.
*/
```

