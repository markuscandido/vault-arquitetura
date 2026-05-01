---
title: Estratégias de Distribuição
tags: [architecture, distribution, performance]
status: detailed
date: 2026-05-01
---

# Estratégias de Distribuição

Estratégias de distribuição lidam com como espalhar uma aplicação por múltiplos nós de processamento (servidores).

## A Primeira Lei dos Objetos Distribuídos
**"Não distribua seus objetos!"**

Martin Fowler argumenta que muitos desenvolvedores distribuem objetos erroneamente (por exemplo, colocando classes diferentes em servidores diferentes) por desempenho. Na realidade, o overhead de chamadas remotas (entre processos ou rede) é ordens de magnitude maior do que chamadas locais, frequentemente levando a um desempenho ruim e designs complexos.

## Interfaces Locais vs. Remotas
- **Interfaces Locais**: Podem ser de granularidade fina (muitos métodos pequenos). Bom para design OO e flexibilidade.
- **Interfaces Remotas**: Devem ser de granularidade grossa (poucas chamadas que realizam muito trabalho). Projetadas para minimizar o número de viagens de ida e volta na rede (roundtrips).

## Clustering
A alternativa preferida para distribuir objetos individuais é o **Clustering**.
- Execute múltiplas cópias de *todo* o processo da aplicação em nós diferentes.
- Cada processo lida com suas próprias requisições usando chamadas locais de granularidade fina.
- A escalabilidade é alcançada adicionando mais nós ao cluster, não dividindo o modelo de objetos.

## Quando Distribuir
Distribua apenas quando necessário:
- **Separando Camadas**: Manter a apresentação em um servidor web e a lógica de domínio em um servidor de aplicação (embora até mesmo isso deva ser examinado).
- **Isolamento Físico**: Quando um recurso específico (como um sistema legado) só pode ser acessado de um nó específico.
- **Requisitos de Fornecedor**: Quando um serviço de terceiros exige uma interface remota.

## Fonte
- [[Livro_PEAA]] (Capítulo 7)
