---
title: Apresentação Separada
tags: [conceito, arquitetura]
status: detalhado
date: 2026-05-01
---

# Apresentação Separada (Separated Presentation)

O princípio de manter a interface do usuário (Apresentação) separada da lógica de negócio central (Domínio).

## Ideia Principal
A Camada de Apresentação depende da Camada de Domínio, mas a **Camada de Domínio nunca deve depender da Camada de Apresentação**.

## Benefícios
- **Suporte a Múltiplas Interfaces**: Você pode construir uma UI Web, um App Mobile e uma CLI sobre a mesma lógica de domínio.
- **Testabilidade**: A lógica de domínio pode ser testada sem a complexidade de uma UI (testes headless).
- **Especialização**: Designers podem focar na UI enquanto engenheiros focam nas regras de negócio.
- **Manutenibilidade**: Mudanças na UI não quebram a lógica de negócio, e vice-versa.

## Padrão de Implementação
- A implementação mais famosa deste princípio é o **[[MVC|Model-View-Controller (MVC)]]**.

## Fonte
- [[Livro_PEAA]] (Capítulo 14, Página 316)
