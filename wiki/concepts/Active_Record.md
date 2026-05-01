---
title: Active Record
tags: [padrao, fonte-de-dados, logica-de-dominio]
status: detalhado
date: 2026-05-01
---

# Active Record

Um objeto que envolve uma linha em uma tabela ou visualização de banco de dados, encapsula o acesso ao banco de dados e adiciona lógica de domínio sobre esses dados.

## Como Funciona
- Combina acesso a dados (como [[Row_Data_Gateway]]) e lógica de negócio na mesma classe.
- A estrutura da classe espelha de perto a estrutura da tabela do banco de dados.
- Os métodos normalmente incluem operações CRUD e cálculos/validações de domínio.

## Quando Usar
- Lógica de domínio de simples a moderadamente complexa.
- Quando há uma correspondência de 1 para 1 entre objetos de domínio e tabelas de banco de dados.
- É a abordagem mais simples para combinar dados e comportamento.

## Desvantagens
- Acopla a [[Domain_Model|Camada de Domínio]] diretamente à [[Camada_de_Fonte_de_Dados]] e ao esquema do banco de dados.

## Fonte
- [[Livro_PEAA]] (Capítulo 3, Página 165)
