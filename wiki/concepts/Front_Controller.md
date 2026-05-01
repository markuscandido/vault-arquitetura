---
title: Front Controller
tags: [pattern, presentation]
status: detailed
date: 2026-05-01
---

# Front Controller

Um único manipulador que centraliza todas as requisições de um site Web.

## Como Funciona
Em vez de ter um [[Page_Controller]] separado para cada URL, um Front Controller usa um único objeto para receber todas as requisições recebidas. Este controlador então delega o trabalho para objetos "comando" (command) ou "ação" (action).

### Responsabilidades
1. **Lógica Centralizada**: Lida com tarefas comuns como segurança, internacionalização, log e roteamento em um só lugar.
2. **Roteamento**: Decodifica a URL e decide qual objeto de ação/comando deve lidar com a lógica de negócio.
3. **Gerenciamento de Visão (View)**: Decide qual visão exibir com base no resultado da ação.

### Implementação
- Geralmente implementado como um **Servlet** (Java) ou um **script global** (PHP/ASP) que atua como o ponto de entrada para toda a aplicação.
- Usa um **Mapa de Comandos** ou um mecanismo de carregamento dinâmico para encontrar a classe de ação correta para uma requisição.

## Benefícios
- **Evita Duplicação**: A lógica comum de manipulação de requisições não é repetida em várias páginas.
- **Facilmente Extensível**: Adicionar novas páginas ou ações requer apenas a adição de uma nova classe de comando e a atualização do mapeamento.

## Fonte
- [[Livro_PEAA]] (Capítulo 14, Página 328)
