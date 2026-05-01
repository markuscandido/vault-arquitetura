---
title: Model-View-Controller (MVC)
tags: [pattern, presentation]
status: detailed
date: 2026-05-01
---

# Model-View-Controller (MVC)

Splits a user interface into three distinct roles: the Model, the View, and the Controller.

## Components
- **Model**: Represents the non-visual logic and data of the application. It is unaware of the View and Controller.
- **View**: Responsible for rendering the Model's data into a user interface.
- **Controller**: Handles user input, translates it into actions on the Model, and decides which View to show next.

## Core Principle: Separated Presentation
The key idea is to keep the Model (domain logic) independent of the UI. This allows for:
- Testing the domain logic without a UI.
- Having multiple UIs (e.g., Web and Mobile) for the same domain logic.

## MVC in the Web
In web applications, MVC is slightly adapted:
- The **View** is the HTML rendered in the browser.
- The **Controller** is the server-side code (Servlet, Action, etc.) that handles the HTTP request.
- The **Model** is the [[Domain_Model|Domain Layer]].

## Source
- [[PEAA_Book]] (Chapter 4, Page 330)
