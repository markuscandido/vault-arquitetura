---
title: Campo de Identidade
tags: [pattern, data-source, structural]
status: detailed
date: 2026-05-01
---

# Campo de Identidade

Salva um campo de ID do banco de dados em um objeto para manter a identidade entre um objeto em memória e uma linha do banco de dados.

## Como Funciona
O Campo de Identidade é um campo simples (geralmente um `long` ou `UUID`) no objeto de domínio que armazena a chave primária da linha correspondente no banco de dados.

## Considerações Principais
- **Chaves Sem Significado vs. Com Significado**: Fowler prefere fortemente chaves sem significado (chaves substitutas/surrogate keys), pois são mais estáveis. Chaves com significado (como números de CPF) podem mudar ou ser reutilizadas, causando pesadelos no mapeamento.
- **Geração de Chaves**:
    - **Gerenciada pelo Banco de Dados**: Usando `AUTO_INCREMENT` ou `IDENTITY`. Fácil, mas torna difícil saber o ID antes de salvar.
    - **Gerenciada pela Aplicação**: Gerando um UUID ou usando uma **Tabela de Chaves** (uma tabela especial que rastreia o próximo ID disponível). Permite que o objeto tenha um ID antes mesmo de ser salvo.

## Fonte
- [[Livro_PEAA]] (Capítulo 12, Página 215)
