---
title: Mapeamento de Chave Estrangeira
tags: [pattern, data-source, structural]
status: detailed
date: 2026-05-01
---

# Mapeamento de Chave Estrangeira

Mapeia uma associação entre objetos para uma referência de chave estrangeira entre tabelas.

## Como Funciona
Em um modelo de objetos, os objetos se referem uns aos outros diretamente. Em um banco de dados relacional, as linhas se referem umas às outras usando chaves estrangeiras.

### 1 para 1 e Muitos para 1
- Para uma referência simples do Objeto A para o Objeto B, você armazena o ID de B na tabela para A.
- Exemplo: Um `Album` tem um `Artist`. A tabela `Albums` tem uma coluna `artist_id`.

### 1 para Muitos
- Como as tabelas relacionais não podem armazenar coleções, você deve "inverter" a referência.
- Exemplo: Um `Album` tem muitos `Tracks`. Em vez de a tabela `Albums` ter uma lista de faixas, a tabela `Tracks` tem uma coluna `album_id`.

## Lidando com Atualizações em Coleções
Quando uma coleção muda (itens adicionados ou removidos), você tem três estratégias principais:
1. **Excluir e Inserir**: Exclua todas as linhas relacionadas existentes e insira o estado atual da coleção. Simples, mas só funciona se os filhos forem [[Dependent_Mapping|Mapeamentos Dependentes]].
2. **Ponteiro de Retorno (Back Pointer)**: Garanta que o objeto filho tenha uma referência para o pai. Isso transforma o relacionamento em um mapeamento simples Muitos-para-1.
3. **Diferenciação (Diffing)**: Compare a coleção atual em memória com o estado do banco de dados e emita apenas os comandos INSERT, UPDATE ou DELETE necessários.

## Fonte
- [[Livro_PEAA]] (Capítulo 12, Página 233)
