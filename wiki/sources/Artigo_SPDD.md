---
title: "Structured-Prompt-Driven Development (SPDD)"
author: "Thoughtworks (via Martin Fowler)"
date: 2026-05-05
tags: [AI, LLM, prompt-engineering, methodology]
source: https://martinfowler.com/articles/structured-prompt-driven/
---

# Structured-Prompt-Driven Development (SPDD)

O artigo apresenta uma metodologia de engenharia para tornar o desenvolvimento assistido por IA (LLMs) governável, revisável e reutilizável em escala organizacional.

## Resumo dos Pontos Principais

- **Prompts como Artefatos de Primeira Classe**: O SPDD propõe que os prompts sejam ativos de engenharia versionados, revisados e mantidos junto ao código-fonte, não apenas interações efêmeras.
- **Governança e Qualidade**: O foco muda de "gerar mais código" para "gerar mudanças governáveis", evitando dívida técnica e desalinhamento.
- **Ciclo de Feedback Fechado**: Inclui fluxos como *Prompt-First Update* (atualizar o prompt antes do código) e *Sync-Back* (sincronizar mudanças manuais de volta ao prompt).
- **Habilidades Humanas**: O desenvolvedor evolui para um "arquiteto de intenções", exigindo forte capacidade de abstração e modelagem de domínio.

## Conceitos Introduzidos

- [[SPDD]]: A metodologia em si.
- [[Canvas_REASONS]]: Estrutura para criação de prompts estruturados.
- **Abstração Primeiro**: Priorizar o design antes da geração de código.
- **Alinhamento**: Tornar explícito o que será feito e as restrições.

## Referências
- Artigo original: [martinfowler.com](https://martinfowler.com/articles/structured-prompt-driven/)
