---
title: Model-View-Controller (MVC)
tags: [padrao, apresentacao]
status: detalhado
date: 2026-05-01
---

# Model-View-Controller (MVC)

Divide uma interface de usuário em três papéis distintos: o Modelo (Model), a Visão (View) e o Controlador (Controller).

## Componentes
- **Modelo**: Representa a lógica não visual e os dados da aplicação. Ele não tem conhecimento da Visão e do Controlador.
- **Visão**: Responsável por renderizar os dados do Modelo em uma interface de usuário.
- **Controlador**: Manipula a entrada do usuário, traduz em ações no Modelo e decide qual Visão mostrar a seguir.

## Princípio Fundamental: Apresentação Separada
A ideia principal é manter o Modelo (lógica de domínio) independente da interface de usuário (UI). Isso permite:
- Testar a lógica de domínio sem uma UI.
- Ter múltiplas UIs (ex: Web e Mobile) para a mesma lógica de domínio.

## MVC na Web
Em aplicações web, o MVC é levemente adaptado:
- A **Visão** é o HTML renderizado no navegador.
- O **Controlador** é o código do lado do servidor (Servlet, Action, etc.) que manipula a requisição HTTP.
- O **Modelo** é a [[Domain_Model|Camada de Domínio]].

## Fonte
- [[Livro_PEAA]] (Capítulo 4, Página 330)
