---
title: Identity Map
tags: [pattern, data-source, behavioral]
status: detailed
date: 2026-05-01
---

# Identity Map

Garante que cada objeto seja carregado apenas uma vez, mantendo cada objeto carregado em um mapa. Busca objetos usando o mapa ao se referir a eles.

## Como Funciona
Quando você carrega dados de um banco de dados, o Identity Map verifica se já possui um objeto com aquele ID.
- Se **sim**: Ele retorna a instância existente.
- Se **não**: Ele cria uma nova instância, coloca-a no mapa e a retorna.

## Por que é Necessário?
Em um banco de dados relacional, você identifica uma linha por sua chave primária. Em um ambiente OO, você identifica um objeto por sua referência ou identidade. Sem um Identity Map, você pode carregar a mesma linha do banco de dados em dois objetos diferentes na memória. Se você modificar ambos, qual deles deve ser salvo? O Identity Map evita este problema de "objeto duplicado".

## Escopo
Identity Maps são geralmente limitados a uma única **Requisição** ou **Transação**. Mantê-los globalmente em várias sessões é perigoso, pois leva a dados obsoletos e vazamentos de memória.

## Fonte
- [[Livro_PEAA]] (Capítulo 11, Página 196)
