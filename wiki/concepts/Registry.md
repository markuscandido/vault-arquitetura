---
title: Registry
tags: [padrao, base]
status: detalhado
date: 2026-05-01
---

# Registry

Um objeto bem conhecido que outros objetos podem usar para encontrar objetos e serviços comuns.

## Como Funciona
Um Registry atua como um ponto global de acesso para encontrar objetos quando você não tem uma referência direta. Frequentemente é implementado usando um **Singleton** ou métodos estáticos.

## Escopos
- **Escopo de Processo**: Dados compartilhados por todo o processo da aplicação.
- **Escopo de Thread**: Dados específicos para a thread atual (ex: uma conexão de banco de dados).
- **Escopo de Sessão**: Dados específicos para uma sessão de usuário.

## Implementação
- **Métodos Estáticos**: Fácil de chamar de qualquer lugar: `Registry.personFinder()`.
- **Interface vs Implementação**: É melhor que o Registry retorne uma interface para que a implementação possa ser trocada (ex: para testes).

## Aviso
Um Registry é efetivamente **Dados Globais**. Use-o como último recurso. Prefira passar dependências através de construtores (Injeção de Dependência) sempre que possível.

## Fonte
- [[Livro_PEAA]] (Capítulo 18, Página 448)
