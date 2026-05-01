---
title: Table Module
tags: [padrao, logica-de-dominio]
status: detalhado
date: 2026-05-01
---

# Table Module

Uma única instância que manipula a lógica de negócio para todas as linhas em uma tabela ou visualização de banco de dados.

## Como Funciona
- Ao contrário do [[Domain_Model]], que possui um objeto por registro, o Table Module possui um objeto por *tabela*.
- Tipicamente trabalha com um [[RecordSet]] (uma estrutura de dados que contém os resultados de uma consulta).
- Os clientes passam um ID de registro para os métodos do Table Module para atuar em dados específicos.

## Benefícios
- **Integração**: Ajusta-se perfeitamente a plataformas que enfatizam RecordSets (como .NET ou sistemas antigos baseados em COM).
- **Estrutura**: Mais organizado que o [[Transaction_Script]], pois a lógica é agrupada por entidade/tabela.

## Desvantagens
- Não é possível usar técnicas OO de granularidade fina, como polimorfismo, para registros individuais.

## Fonte
- [[Livro_PEAA]] (Capítulo 2, Página 134)
