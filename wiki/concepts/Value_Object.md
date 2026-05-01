---
title: Value Object
tags: [padrao, base]
status: detalhado
date: 2026-05-01
---

# Value Object

Um objeto pequeno e simples, como dinheiro ou um intervalo de datas, cuja igualdade não é baseada em identidade.

## Como Funciona
Dois Value Objects são considerados "o mesmo" se seus valores forem iguais, mesmo que sejam instâncias diferentes na memória.
- Exemplo: Duas notas de R$ 5 são funcionalmente idênticas para a maioria dos propósitos de negócio.
- Exemplo: Dois objetos `DateRange` representando "2026-01-01 a 2026-01-02" são iguais.

## Regras Centrais
1. **Imutabilidade**: Uma vez criado, um Value Object nunca deve mudar. Para "alterá-lo", você cria uma nova instância.
2. **Igualdade Baseada em Valor**: Sobrescreva os métodos `equals()` e `hashCode()` para comparar campos em vez de endereços de memória.

## Benefícios
- **Sem Efeitos Colaterais**: Como são imutáveis, você pode passá-los sem se preocupar que outra parte do sistema altere seu valor.
- **Simplifica a Lógica**: Você não precisa rastrear identidade para conceitos pequenos.

## Fonte
- [[Livro_PEAA]] (Capítulo 18, Página 453)
