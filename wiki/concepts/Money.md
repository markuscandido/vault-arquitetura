---
title: Money
tags: [pattern, base]
status: detailed
date: 2026-05-01
---

# Money

Representa um valor monetário, encapsulando a quantia e a moeda.

## Por que é Necessário?
Usar números de ponto flutuante simples (como `double` ou `float`) para dinheiro é um grande antipadrão devido a erros de arredondamento. Um objeto `Money` garante que os cálculos sejam precisos e que moedas diferentes não sejam misturadas acidentalmente.

## Principais Características
- **Quantia e Moeda**: Armazena tanto o valor numérico (geralmente como um `long` da menor unidade, como centavos) quanto o tipo de moeda.
- **Operações Aritméticas**: Fornece métodos para adição, subtração e comparação que verificam a compatibilidade da moeda.
- **Arredondamento e Alocação**: Encapsula a lógica para arredondamento e alocação de dinheiro (ex: dividir $0,05 em duas contas 70/30 sem perder um centavo).
- **Formatação e Parsing**: Lida com a exibição e entrada de dados ciente da moeda.

## Dicas de Implementação
- Use a semântica de **[[Value_Object]]** (imutabilidade).
- Forneça um método `allocate` para lidar com o "Enigma de Foemmel" (alocação de centavos entre vários alvos).

## Fonte
- [[Livro_PEAA]] (Capítulo 18, Página 455)
