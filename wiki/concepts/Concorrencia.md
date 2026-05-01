---
title: Concorrência
tags: [conceito, infraestrutura]
status: detalhado
date: 2026-05-01
---

# Concorrência

A concorrência ocorre quando múltiplos usuários ou processos tentam acessar e modificar os mesmos dados simultaneamente.

## Problemas
- **Atualizações Perdidas (Lost Updates)**: O Usuário A e o Usuário B leem o mesmo registro; o Usuário A salva, depois o Usuário B salva, sobrescrevendo as alterações do Usuário A.
- **Leituras Inconsistentes (Inconsistent Reads)**: Ler dados que estão no meio de uma alteração por outro processo.

## Propriedades ACID
As transações devem ser Atômicas, Consistentes, Isoladas e Duráveis.

## Concorrência Offline
Em aplicações corporativas, uma transação de negócio (ex: um checkout de múltiplas telas) frequentemente abrange múltiplas transações de sistema (requisições HTTP). Como as transações de banco de dados não devem permanecer abertas por tanto tempo, usamos **Bloqueio Offline (Offline Locking)**:
- [[Optimistic_Offline_Lock]]: Assume que colisões são raras. Verifica se os dados mudaram antes de salvar.
- [[Pessimistic_Offline_Lock]]: Assume que colisões são prováveis. Impede que outros acessem os dados enquanto você os possui.

## Fonte
- [[Livro_PEAA]] (Capítulo 5)
