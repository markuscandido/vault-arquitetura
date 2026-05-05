---
title: Arquitetura
tags: [conceito, arquitetura]
status: detalhado
date: '2026-05-01'
---

# Arquitetura

Arquitetura é um termo subjetivo na engenharia de software, frequentemente usado para denotar "coisas importantes" sobre o design de um sistema.

## Definições
1. **Decomposição de Alto Nível**: A divisão de um sistema em suas partes principais.
2. **Decisões Difíceis de Mudar**: Decisões que são difíceis de alterar uma vez que a implementação progrediu.
3. **Entendimento Compartilhado**: (Ralph Johnson) O design de um sistema compartilhado pelos desenvolvedores especialistas em um projeto.

## Subjetividade
A arquitetura depende do que é percebido como difícil de mudar. Se uma decisão for considerada facilmente reversível, ela pode perder seu status "arquitetural".

## Foco no PEAA
No contexto do [[Livro_PEAA]], o foco é em:
- Decompor aplicações em [[Camadas]].
- Como essas camadas interagem.

## Evolução e Abordagens Modernas

### Arquitetura Orientada por Prompts (SPDD)
Com a ascensão das IAs Generativas, a arquitetura ganha uma nova dimensão: a **Arquitetura de Intenções**. Metodologias como o [[SPDD]] propõem que o design e as decisões arquiteturais sejam capturados em prompts estruturados (como o [[Canvas_REASONS]]), tornando a intenção do arquiteto um artefato versionável e governável que direciona a geração automática de código.

## Fonte
- [[Livro_PEAA]] (Introdução)
- [[Artigo_SPDD]]
