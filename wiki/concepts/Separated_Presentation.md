---
title: Separated Presentation
tags: [concept, architecture]
status: detailed
date: 2026-05-01
---

# Separated Presentation

The principle of keeping the user interface (Presentation) separate from the core business logic (Domain).

## Core Idea
The Presentation Layer depends on the Domain Layer, but the **Domain Layer must never depend on the Presentation Layer**.

## Benefits
- **Multi-Interface Support**: You can build a Web UI, a Mobile App, and a CLI on top of the same domain logic.
- **Testability**: Domain logic can be tested without the complexity of a UI (headless testing).
- **Specialization**: Designers can focus on the UI while engineers focus on business rules.
- **Maintainability**: Changes in the UI don't break the business logic, and vice-versa.

## Implementation Pattern
- The most famous implementation of this principle is **[[MVC|Model-View-Controller (MVC)]]**.

## Source
- [[PEAA_Book]] (Chapter 14, Page 316)
