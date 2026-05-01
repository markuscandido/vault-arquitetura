---
title: Template View
tags: [padrao, apresentacao]
status: detalhado
date: 2026-05-01
---

# Template View

Renderiza informações em HTML incorporando marcadores em uma página HTML.

## Como Funciona
Um Template View começa com a saída desejada (HTML) e permite que você "conecte" dados dinâmicos vindos do modelo.
- **Scriptlets**: Pequenos pedaços de código incorporados diretamente no HTML (ex: `<?php echo $name; ?>`).
- **Tags Customizadas**: Usando marcadores específicos que são substituídos pelo servidor (ex: `<jsp:getProperty ... />`).

## Benefícios
- **Amigável a WYSIWYG**: Designers frequentemente podem abrir o template em um editor e ver aproximadamente como ele ficará.
- **Natural para a Web**: A maioria dos desenvolvedores está confortável com esta abordagem "HTML-first".

## Desvantagens
- **Sopa de Tags (Tag Soup)**: A lógica pode facilmente vazar para a visão, tornando-a bagunçada e difícil de testar.

## Fonte
- [[Livro_PEAA]] (Capítulo 14, Página 333)
