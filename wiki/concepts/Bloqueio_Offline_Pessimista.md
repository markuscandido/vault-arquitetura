---
title: Bloqueio Offline Pessimista
tags: [padrao, concorrencia]
status: detalhado
date: 2026-05-01
---

# Bloqueio Offline Pessimista

Previne conflitos entre transações de negócio concorrentes permitindo que apenas uma pessoa acesse os dados por vez.

## Como Funciona
- Quando um usuário começa a editar, a aplicação adquire um bloqueio para esses dados em um gerenciador de bloqueios central (ou uma tabela de bloqueio no BD).
- Outros usuários que tentam editar os mesmos dados são bloqueados ou recebem um erro imediatamente.
- O bloqueio é liberado quando a transação de negócio é finalizada (salva ou cancelada).

## Benefícios
- Os usuários descobrem cedo se não podem editar os dados.
- Evita perda de trabalho ao final de uma transação.

## Desvantagens
- Menor concorrência.
- Mais difícil de implementar (precisa lidar com timeouts de bloqueio e sessões abandonadas).

## Fonte
- [[Livro_PEAA]] (Capítulo 5, Página 401)
