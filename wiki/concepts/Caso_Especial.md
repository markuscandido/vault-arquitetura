---
title: Caso Especial
tags: [padrao, base]
status: detalhado
date: 2026-05-01
---

# Caso Especial (Special Case)

Uma subclasse que fornece comportamento especial para casos particulares.

## Por que é Necessário?
Ponteiros nulos (null pointers) são uma causa comum de erros, e verificações "if (obj == null)" causam duplicação de código. Um objeto de Caso Especial fornece um comportamento de "não fazer nada" ou um comportamento padrão que corresponde à interface esperada, permitindo que o restante do código o trate como um objeto normal.

## Exemplos
- **Objeto Nulo (Null Object)**: Em vez de retornar `null` para um cliente ausente, retorne um objeto `ClienteNulo` onde `getNome()` retorna "Ocupante" e `getDesconto()` retorna 0.
- **Cliente Desconhecido**: Para usuários ainda não identificados.
- **Infinito**: Em sistemas numéricos.

## Benefícios
- **Remove Condicionais**: Elimina verificações de nulidade repetitivas.
- **Polimorfismo**: Aproveita o design orientado a objetos para lidar com casos de borda de forma limpa.

## Fonte
- [[Livro_PEAA]] (Capítulo 18, Página 462)
