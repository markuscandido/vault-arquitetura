---
title: Data Mapper
tags: [pattern, data-source]
status: detailed
date: 2026-05-01
---

# Data Mapper

Uma camada de mapeadores que move dados entre objetos e um banco de dados, mantendo-os independentes um do outro e do próprio mapeador.

## Como Funciona
- Os objetos do [[Domain_Model]] ignoram completamente o banco de dados.
- O Data Mapper lida com a tradução entre a rede de objetos e as tabelas relacionais.
- É responsável por carregar objetos do banco de dados e salvar seu estado de volta.

## Benefícios
- **Desacoplamento Total**: O domínio e o banco de dados podem evoluir independentemente.
- **Mapeamento Complexo**: Lida com casos onde a estrutura do objeto é muito diferente do esquema do banco de dados (ex: herança, associações complexas).

## Desvantagens
- **Complexidade**: É o padrão de mapeamento mais complexo de implementar.
- Frequentemente requer técnicas avançadas como [[Identity_Map]], [[Unit_of_Work]] e [[Lazy_Load]].

## Fonte
- [[Livro_PEAA]] (Capítulo 3, Página 170)
