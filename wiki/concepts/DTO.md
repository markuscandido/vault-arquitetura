---
title: Objeto de Transferência de Dados (DTO)
tags: [pattern, distribution]
status: detailed
date: 2026-05-01
---

# Data Transfer Object (DTO)

Um objeto que carrega dados entre processos para reduzir o número de chamadas de métodos.

## Por que é Necessário?
Em um sistema distribuído, chamadas remotas são caras. Se você usar uma interface de granularidade fina (muitos pequenos getters/setters), sofrerá com latência extrema. Um DTO permite reunir todos os dados necessários para uma tarefa específica e enviá-los em uma única chamada remota.

## Características
- **Apenas Dados**: DTOs devem conter apenas campos de dados e getters/setters simples. Eles **não possuem lógica de negócio**.
- **Serializável**: DTOs devem ser facilmente convertidos para um formato (como XML, JSON ou Binário) que possa ser enviado pela rede.
- **Estrutura Plana**: Frequentemente "achatados" (flattened) para simplificar os dados transferidos, embora possam conter DTOs aninhados para estruturas complexas.

## Implementação
- Frequentemente gerados automaticamente a partir de objetos de domínio ou esquemas de banco de dados.
- Em Java, costumam implementar `Serializable`. Em .NET, costumam ser `structs` ou `classes` simples.

## Fonte
- [[Livro_PEAA]] (Capítulo 15, Página 380)
