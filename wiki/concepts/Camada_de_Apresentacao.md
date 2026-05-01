---
title: Camada de Apresentação
tags: [arquitetura, camada]
status: detalhado
date: 2026-05-01
---

# Camada de Apresentação

A Camada de Apresentação é responsável por lidar com a interação entre o usuário e o software. Isso pode variar de uma interface de linha de comando (CLI) até uma interface gráfica rica (GUI) ou uma interface baseada na web.

## Responsabilidades
- **Exibir Informações**: Apresentar dados da [[Camada_de_Dominio]] ao usuário.
- **Capturar Entrada**: Receber comandos e dados do usuário.
- **Interpretar Entrada**: Traduzir as ações do usuário em chamadas para a [[Camada_de_Dominio]] ou [[Camada_de_Fonte_de_Dados]].

## Padrões Principais
O padrão mais fundamental em apresentação é o **[[MVC]]**, que separa a representação interna da informação das formas como essa informação é apresentada e aceita pelo usuário.

### Padrões de Apresentação Web
- **[[Template_View]]**: Renderizar informações em HTML usando marcadores em um template.
- **Transform View**: Usar uma transformação (como XSLT) para transformar dados de domínio em HTML.
- **[[Front_Controller]]**: Um único manipulador para todas as requisições de um site.
- **Application Controller**: Gerenciar o fluxo de uma aplicação de múltiplas telas.

## Fonte
- [[Livro_PEAA]] (Capítulo 1 & 4)
