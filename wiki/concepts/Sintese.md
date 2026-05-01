---
title: Juntando Tudo (Síntese)
tags: [arquitetura, estrategia]
status: detalhado
date: 2026-05-01
---

# Juntando Tudo (Síntese)

Este capítulo fornece uma estrutura para escolher os padrões certos para uma aplicação com base em suas necessidades específicas.

## Escolhendo Padrões de Lógica de Domínio
A escolha depende da complexidade da lógica de negócio:
- **[[Transaction_Script]]**: Melhor para lógica simples. Baixa sobrecarga, fácil de entender.
- **[[Table_Module]]**: Bom meio-termo, especialmente se estiver trabalhando com plataformas ricas em [[RecordSet]] (.NET).
- **[[Domain_Model]]**: Melhor para lógica complexa e em evolução. Curva de aprendizado alta, mas escala bem em manutenibilidade.

## Escolhendo Padrões de Mapeamento de Dados
O padrão de mapeamento é frequentemente ditado pela lógica de domínio escolhida:
- **[[Transaction_Script]]** -> [[Table_Data_Gateway]] ou [[Row_Data_Gateway]].
- **[[Table_Module]]** -> [[Table_Data_Gateway]].
- **[[Domain_Model]] Simples** -> [[Active_Record]].
- **[[Domain_Model]] Rico** -> [[Data_Mapper]].

## Arquitetando a Apresentação
- Use **[[MVC]]** como base.
- Para apps web, decida entre **Template View** (simples, estilo script) ou **Transform View** (mais formal, bom para múltiplas saídas).
- Use um **Front Controller** para centralizar a lógica comum (segurança, log).

## Estratégia de Concorrência
- Use por padrão o **[[Bloqueio_Offline_Otimista]]** para melhor escalabilidade e simplicidade.
- Use o **[[Bloqueio_Offline_Pessimista]]** apenas quando o custo de uma colisão (trabalho perdido) for inaceitavelmente alto para o usuário.

## Tabela de Resumo
| Complexidade da Lógica | Padrão de Domínio | Padrão de Mapeamento |
| :--- | :--- | :--- |
| Baixa | [[Transaction_Script]] | Data Gateway |
| Média | [[Table_Module]] | [[Table_Data_Gateway]] |
| Alta | [[Domain_Model]] | [[Data_Mapper]] |

## Fonte
- [[Livro_PEAA]] (Capítulo 8)
