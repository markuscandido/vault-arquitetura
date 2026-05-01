---
title: Camadas
tags: [arquitetura, conceito]
status: detalhado
date: 2026-05-01
---

# Camadas

O camadeamento (layering) é a técnica mais comum para decompor sistemas de software complexos. Ele organiza os subsistemas como um "bolo de camadas", onde cada camada repousa sobre uma camada inferior.

## Princípios Fundamentais
- **Direção da Dependência**: Camadas superiores usam serviços de camadas inferiores, mas as camadas inferiores não têm conhecimento das camadas superiores.
- **Encapsulamento**: As camadas geralmente escondem suas subcamadas (ex: a Camada 4 usa a Camada 3, que usa a Camada 2, mas a Camada 4 não tem conhecimento da Camada 2).

## Benefícios
- **Compreensibilidade**: Você pode entender uma única camada sem conhecer o sistema todo.
- **Substituibilidade**: As camadas podem ser substituídas por implementações alternativas dos mesmos serviços.
- **Minimização de Dependências**: Alterações em camadas inferiores (ex: hardware físico) não afetam necessariamente as camadas superiores.
- **Padronização**: As camadas fornecem pontos naturais para definir padrões (ex: TCP/IP).
- **Reutilização**: Uma vez construída, uma camada inferior pode ser usada por múltiplos serviços de nível superior.

## Desvantagens
- **Mudanças em Cascata**: Algumas mudanças (ex: adicionar um campo) devem percorrer todas as camadas.
- **Sobrecarga de Desempenho (Overhead)**: A transformação de dados entre camadas pode tornar o sistema mais lento, embora os benefícios do encapsulamento frequentemente compensem isso.

## As Três Camadas Principais
Em Aplicações Corporativas, a decomposição padrão é:
1. **[[Camada_de_Apresentacao]]**: Interface com o usuário (Web, GUI, CLI).
2. **[[Camada_de_Dominio]]**: Lógica de negócio, cálculos e regras.
3. **[[Camada_de_Fonte_de_Dados]]**: Interação com bancos de dados, sistemas de mensageria e outras APIs.

## Fonte
- [[Livro_PEAA]] (Capítulo 1)
