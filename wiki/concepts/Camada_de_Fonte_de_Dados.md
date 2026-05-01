---
title: Camada de Fonte de Dados
tags: [architecture, layer]
status: detailed
date: 2026-05-01
---

# Camada de Fonte de Dados

A Camada de Fonte de Dados é responsável por se comunicar com outros sistemas que realizam tarefas em nome da aplicação. Isso normalmente envolve bancos de dados, sistemas de mensageria e APIs externas.

## Responsabilidades
- **Persistência de Dados**: Salvar e recuperar dados de armazenamento de longo prazo (bancos de dados relacionais, NoSQL, etc.).
- **Integração**: Interface com sistemas legados ou serviços de terceiros.
- **Abstração**: Fornecer uma API limpa para a [[Domain_Model|Camada de Domínio]] para que ela não tenha que lidar com as especificidades de SQL, protocolos de rede ou formatos de dados externos.

## Principais Padrões de Mapeamento
Dependendo da complexidade do domínio e do grau de desacoplamento necessário, diferentes padrões são usados:
- [[Table_Data_Gateway]]: Um objeto para uma tabela inteira.
- [[Row_Data_Gateway]]: Um objeto para uma única linha em uma tabela.
- [[Active_Record]]: Um objeto que envolve uma linha e inclui lógica de negócio.
- [[Data_Mapper]]: Uma camada que move dados entre objetos e um banco de dados enquanto os mantém independentes.

## Fonte
- [[Livro_PEAA]] (Capítulos 1 e 3)
