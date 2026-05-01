---
title: Page Controller
tags: [padrao, apresentacao]
status: detalhado
date: 2026-05-01
---

# Page Controller

Um objeto que manipula uma requisição para uma página ou ação específica em um site Web.

## Como Funciona
Um Page Controller é o ponto de entrada para uma URL ou ação específica. Ele coordena a lógica necessária para satisfazer a requisição e decide qual visão exibir a seguir.

### Responsabilidades
1. **Extrair Dados**: Lê parâmetros da requisição HTTP (GET/POST).
2. **Invocar Lógica**: Chama os objetos apropriados na [[Camada_de_Dominio]].
3. **Selecionar Visão**: Determina qual template ou página deve ser usada para a resposta.
4. **Preparar Dados**: Coloca os resultados da lógica de domínio em um formato que a visão possa ler facilmente (frequentemente usando um **Objeto Auxiliar/Helper**).

### Estilos de Implementação
- **Baseado em Script**: Usando um servlet, script CGI ou uma classe específica para manipular a lógica antes de transferir o controle para uma visão.
- **Baseado em Server Page**: Usando uma página ASP, JSP ou PHP que contém lógica no topo (frequentemente delegada a uma classe "Code Behind" ou "Helper" para evitar a "sopa de tags").

## Quando Usar
- Melhor para sites com complexidade de navegação de simples a moderada.
- É a maneira padrão e "natural" de estruturar aplicações web onde cada página tem seu próprio manipulador.

## Comparação
- vs. [[Front_Controller]]: Page Controller possui um manipulador por página/ação, enquanto o Front Controller possui um único manipulador para toda a aplicação.

## Fonte
- [[Livro_PEAA]] (Capítulo 14, Página 318)
