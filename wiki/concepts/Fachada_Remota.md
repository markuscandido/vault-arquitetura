---
title: Fachada Remota
tags: [padrao, distribuicao]
status: detalhado
date: 2026-05-01
---

# Fachada Remota (Remote Facade)

Fornece uma fachada de granularidade grossa (coarse-grained) sobre uma rede de objetos de granularidade fina (fine-grained) para melhorar a eficiência em uma rede.

## Como Funciona
Se você tem um [[Domain_Model]] com muitos objetos pequenos e associações, você não quer que clientes remotos interajam com eles diretamente. Em vez disso, você cria uma **Fachada Remota** que oferece alguns métodos de alto nível.
- A Fachada traduz uma única chamada de granularidade grossa em muitas chamadas de granularidade fina para os objetos de domínio locais.
- Frequentemente utiliza [[DTO|DTOs]] para retornar dados ao cliente.

## Princípio Fundamental
"Não distribua seus objetos" (A Primeira Lei da Distribuição). Se você PRECISA distribuir, use uma Fachada Remota para preencher a lacuna entre a lógica de domínio de granularidade fina e a comunicação remota de granularidade grossa.

## Fonte
- [[Livro_PEAA]] (Capítulo 15, Página 368)
