---
title: "Canvas REASONS"
tags: [prompt-engineering, framework, SPDD]
sources: [Artigo_SPDD]
---

# Canvas REASONS

O **Canvas REASONS** é uma estrutura de sete partes projetada para guiar a criação de prompts estruturados dentro da metodologia [[SPDD]]. Ele garante que a intenção do desenvolvedor seja comunicada de forma completa e segura para a IA.

## Estrutura (Componentes)

| Sigla | Termo (Original) | Descrição |
| :--- | :--- | :--- |
| **R** | Requirements | Requisitos funcionais e critérios de aceitação. |
| **E** | Entities | Entidades de domínio, seus atributos e relacionamentos. |
| **A** | Approach | Estratégia de solução, padrões de design (ex: [[MVC]], [[Unit_of_Work]]). |
| **S** | Structure | Onde a mudança se encaixa no sistema (pastas, arquivos, dependências). |
| **O** | Operations | Passos concretos de implementação e como testá-los. |
| **N** | Norms | Padrões de engenharia (nomenclatura, observabilidade, estilo). |
| **S** | Safeguards | Limites inegociáveis (segurança, performance, invariantes). |

## Benefícios

- **Reprodutibilidade**: Prompts estruturados geram resultados mais consistentes.
- **Auditabilidade**: É possível revisar a intenção antes de gerar o código.
- **Redução de Alucinação**: Ao fornecer contexto estruturado (Entidades, Normas, Salvaguardas), a IA tem menos espaço para decisões arbitrárias.

## Páginas Relacionadas
- [[SPDD]]
- [[Arquitetura]]
