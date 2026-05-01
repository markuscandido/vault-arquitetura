---
title: Camada de Serviço
tags: [padrao, logica-de-dominio, arquitetura]
status: detalhado
date: 2026-05-01
---

# Camada de Serviço (Service Layer)

Define o limite de uma aplicação com uma camada de serviços que estabelece um conjunto de operações disponíveis e coordena a resposta da aplicação em cada operação.

## Como Funciona
Uma Camada de Serviço situa-se acima da [[Camada_de_Dominio]] (seja [[Domain_Model]] ou [[Table_Module]]) e atua como uma porta de entrada para diferentes tipos de clientes (UI Web, Web Services, CLI).

### Tipos de Lógica de Negócio
1. **Lógica de Domínio**: Lógica pura relacionada ao problema de negócio (ex: como calcular a receita). Isso pertence à Camada de Domínio.
2. **Lógica de Aplicação**: Responsabilidades relacionadas ao fluxo de trabalho da aplicação (ex: notificar administradores, coordenar múltiplos objetos de domínio). Isso pertence à Camada de Serviço.

### Variantes de Implementação
- **Fachada de Domínio (Domain Facade)**: Uma camada fina de fachadas sobre um [[Domain_Model]]. As classes da camada de serviço não implementam lógica de negócio elas mesmas; elas apenas expõem as capacidades do modelo de domínio.
- **Script de Operação (Operation Script)**: As classes de serviço implementam a lógica de aplicação diretamente, mas delegam a lógica específica de domínio para os objetos de domínio.

## Remoto vs. Local
Embora as interfaces da Camada de Serviço sejam frequentemente de granularidade grossa e adequadas para invocação remota, Fowler aconselha começar com a invocação **local**. Adicione uma [[Fachada_Remota]] apenas se a distribuição física for verdadeiramente necessária.

## Benefícios
- **Lógica Centralizada**: Evita a duplicação da lógica de aplicação em diferentes controladores.
- **Controle de Transação**: Fornece um lugar natural para iniciar e confirmar (commit) transações.
- **Limite Claro**: Define exatamente o que a aplicação pode fazer da perspectiva de um cliente.

## Quando Usar
- Quando múltiplos tipos de clientes acessam a mesma lógica.
- Quando as respostas aos casos de uso envolvem coordenação complexa de múltiplos recursos ou notificações.
- *Não é necessário* para aplicações muito simples com apenas um tipo de cliente e operações CRUD simples.

## Fonte
- [[Livro_PEAA]] (Capítulo 9, Página 141)
