---
title: Row Data Gateway
tags: [padrao, fonte-de-dados]
status: detalhado
date: 2026-05-01
---

# Row Data Gateway

Um objeto que atua como um Gateway para um único registro em uma fonte de dados. Existe uma instância por linha.

## Como Funciona
- O objeto possui um campo para cada coluna na tabela do banco de dados.
- Ele encapsula o acesso ao registro (ex: getters/setters), mas não contém **nenhuma lógica de negócio**.
- Geralmente fornece um método `update()` ou `insert()` para persistir seu estado.

## Quando Usar
- Quando você deseja separar o acesso a dados da lógica de negócio, mas ainda quer uma visão orientada a objetos dos registros.
- Frequentemente usado com [[Transaction_Script]].

## Fonte
- [[Livro_PEAA]] (Capítulo 3, Página 160)
