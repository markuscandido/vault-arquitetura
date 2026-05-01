---
title: Unit of Work
tags: [padrao, fonte-de-dados, comportamental]
status: detalhado
date: 2026-05-01
---

# Unit of Work

Mantém uma lista de objetos afetados por uma transação de negócio e coordena a escrita das mudanças e a resolução de problemas de concorrência.

## Como Funciona
Uma Unit of Work rastreia tudo o que você faz durante uma transação de negócio que possa afetar o banco de dados. Quando você termina, a Unit of Work descobre tudo o que precisa ser feito para alterar o banco de dados como resultado do seu trabalho.

### Responsabilidades Chave
1. **Rastreamento de Mudanças**: Rastreia quais objetos são novos, sujos (modificados) ou excluídos.
2. **Coordenação do Commit**: Quando `commit()` é chamado, ela abre uma transação de banco de dados e executa todos os comandos INSERT, UPDATE e DELETE necessários.
3. **Consistência**: Garante que todas as mudanças sejam escritas ou nenhuma seja, mantendo as propriedades ACID da transação de negócio.

## Benefícios
- **Reduz Chamadas ao Banco de Dados**: Ao agrupar atualizações, minimiza o número de viagens de ida e volta ao banco de dados.
- **Simplifica a Lógica de Domínio**: Os desenvolvedores não precisam se lembrar de chamar `save()` em cada objeto; a Unit of Work cuida disso.
- **Gerenciamento de Concorrência**: É um lugar natural para implementar verificações de [[Bloqueio_Offline_Otimista]].

## Comparação
- Sem uma Unit of Work, você poderia atualizar o banco de dados toda vez que alterasse um objeto de domínio, levando a muitas transações pequenas e ineficientes.

## Fonte
- [[Livro_PEAA]] (Capítulo 11, Página 187)
