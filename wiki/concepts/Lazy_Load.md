---
title: Lazy Load
tags: [pattern, data-source, behavioral]
status: detailed
date: 2026-05-01
---

# Lazy Load

Um objeto que não contém todos os dados que você precisa, mas sabe como obtê-los.

## Por que é Necessário?
Quando você carrega um objeto do banco de dados, frequentemente carrega seus objetos associados. Se você tiver uma teia complexa de objetos, carregar um poderia desencadear o carregamento de todo o banco de dados. O Lazy Load interrompe este processo carregando apenas o objeto "raso" e recuperando o restante apenas quando acessado.

## Variedades de Implementação
1. **Inicialização Preguiçosa (Lazy Initialization)**: Cada acesso a um campo verifica se ele é nulo e o carrega se for.
2. **Proxy Virtual**: Um objeto que se parece com o objeto real, mas só carrega o objeto real quando um método é chamado.
3. **Value Holder**: Um objeto genérico que envolve os dados reais e os carrega sob demanda.
4. **Fantasma (Ghost)**: Um objeto que é parcialmente carregado (geralmente apenas seu ID) e carrega o restante de seus campos na primeira vez que qualquer campo é acessado.

## Desvantagens
- **Problema de Seleção N+1**: Se você carregar objetos preguiçosamente em um loop, pode acabar com muitas pequenas consultas ao banco de dados em vez de um único join.

## Fonte
- [[Livro_PEAA]] (Capítulo 11, Página 200)
