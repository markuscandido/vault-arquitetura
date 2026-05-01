---
title: Domain Model
tags: [pattern, domain-logic, oo]
status: detailed
date: '2026-05-01'
---

# Domain Model

Um modelo de objetos do domínio que incorpora tanto comportamento quanto dados.

## Como Funciona
- Cria uma rede de objetos interconectados, onde cada objeto representa alguma parte individual significativa do negócio (ex: um Contrato específico, uma Fatura).
- A lógica de negócio é distribuída entre esses objetos.
- Utiliza técnicas de OO como herança, estratégias (strategies) e padrões de estado (state patterns) para lidar com a complexidade.

## Quando Usar
- Lógica de negócio complexa e em constante mudança.
- Quando você deseja aproveitar os benefícios da OO para manutenibilidade a longo prazo.

## Custos
- **Complexidade**: Curva de aprendizado acentuada para desenvolvedores não familiarizados com modelos de objetos ricos.
- **Mapeamento de Dados**: Requer uma camada de mapeamento sofisticada (ex: [[Data_Mapper]]) para sincronizar objetos com bancos de dados relacionais.

## Comparação
- vs. [[Transaction_Script]]: Melhor para lógica complexa; lida com duplicação através de abstrações OO.
- vs. [[Table_Module]]: O Domain Model possui uma instância para *cada* registro (ex: um objeto para *cada* Contrato), enquanto o Table Module possui um objeto para *todos* os registros em uma tabela.

## Fonte
- [[Livro_PEAA]] (Capítulo 2, Página 126)
