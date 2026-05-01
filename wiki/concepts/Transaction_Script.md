---
title: Transaction Script
tags: [padrao, logica-de-dominio]
status: detalhado
date: 2026-05-01
---

# Transaction Script

Organiza a lógica de negócio por procedimentos, onde cada procedimento manipula uma única requisição da apresentação.

## Como Funciona
- Cada parte da lógica é um procedimento (script) que executa todos os passos necessários para uma transação de negócio específica.
- Interage diretamente com o banco de dados ou através de um [[Gateway_de_Dados]] simples.
- Os dados são passados em estruturas simples (DTOs, RecordSets).

## Quando Usar
- Aplicações simples com lógica de negócio direta.
- Quando a lógica não justifica a sobrecarga de um modelo de objetos complexo.

## Comparação
- vs. [[Domain_Model]]: Muito mais simples de implementar, mas difícil de manter à medida que a complexidade da lógica cresce devido à duplicação e falta de estrutura OO.
- vs. [[Table_Module]]: Menos estruturado que o Table Module, pois a lógica está ligada a ações em vez de tabelas.

## Fonte
- [[Livro_PEAA]] (Capítulo 2, Página 120)
