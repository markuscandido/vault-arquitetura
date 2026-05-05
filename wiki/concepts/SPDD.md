---
title: "Structured Prompt-Driven Development (SPDD)"
tags: [methodology, AI, LLM]
sources: [Artigo_SPDD]
---

# Structured Prompt-Driven Development (SPDD)

O **Structured Prompt-Driven Development (SPDD)** é uma metodologia de engenharia de software que utiliza LLMs (Large Language Models) de maneira disciplinada e governável. Em vez de interações ad-hoc com chat, o SPDD trata os prompts como artefatos de entrega de primeira classe.

## Princípios Fundamentais

1. **Prompts como Artefatos**: Prompts devem ser versionados (Git), revisados (Code Review) e testados como qualquer outro componente do sistema.
2. **Abstração Primeiro**: O design do sistema, as entidades e as interações são definidos antes da geração do código.
3. **Governança**: Uso de estruturas como o [[Canvas_REASONS]] para garantir que todos os aspectos necessários (segurança, normas, requisitos) sejam considerados.

## Fluxo de Trabalho

- **Prompt-First Update**: Ao identificar um erro ou necessidade de mudança, altera-se primeiro o prompt para que a IA gere a correção/melhoria.
- **Sync-Back Refactoring**: Se um desenvolvedor fizer uma melhoria estrutural manualmente no código, essa mudança deve ser refletida de volta no prompt original para manter a consistência.

## Impacto na Arquitetura

O SPDD exige que o desenvolvedor atue mais como um **arquiteto de intenções**, focando na modelagem correta do domínio e na definição de limites claros, enquanto a IA cuida da implementação de baixo nível seguindo as diretrizes estabelecidas.

## Páginas Relacionadas
- [[Canvas_REASONS]]
- [[Arquitetura]]
