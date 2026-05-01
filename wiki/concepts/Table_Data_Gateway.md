---
title: Table Data Gateway
tags: [padrao, fonte-de-dados]
status: detalhado
date: 2026-05-01
---

# Table Data Gateway

Um objeto que atua como um Gateway para uma tabela de banco de dados. Uma instância manipula todas as linhas na tabela.

## Como Funciona
- Fornece métodos para operações CRUD (Create, Read, Update, Delete) em uma tabela específica.
- Cada consulta SQL para essa tabela é encapsulada dentro deste objeto.
- Tipicamente retorna um [[RecordSet]] ou estruturas de dados simples.

## Quando Usar
- Ao usar [[Transaction_Script]] ou [[Table_Module]] para a lógica de domínio.
- Fornece uma maneira simples de organizar o código SQL.

## Fonte
- [[Livro_PEAA]] (Capítulo 3, Página 151)
