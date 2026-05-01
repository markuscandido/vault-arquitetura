---
title: Bloqueio Offline Otimista
tags: [padrao, concorrencia]
status: detalhado
date: 2026-05-01
---

# Bloqueio Offline Otimista

Previne conflitos entre transações de negócio concorrentes detectando um conflito e revertendo a transação.

## Como Funciona
- Cada registro possui um número de versão ou carimbo de data/hora (timestamp).
- Quando você lê os dados, você mantém a versão.
- Quando você salva os dados, você verifica se a versão no banco de dados é a mesma que você possui.
- `UPDATE table SET ... WHERE id = @id AND version = @old_version`.
- Se 0 linhas forem atualizadas, significa que outra pessoa alterou os dados, e você deve lidar com o conflito (geralmente informando o usuário).

## Benefícios
- Alta concorrência (sem bloqueios de longo prazo).
- Fácil de implementar.

## Desvantagens
- O usuário só descobre o conflito ao final de seu trabalho.

## Fonte
- [[Livro_PEAA]] (Capítulo 5, Página 392)
