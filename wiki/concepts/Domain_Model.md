---
title: Domain Model
tags: [pattern, domain-logic, oo]
status: detailed
date: '2026-05-01'
---

# Domain Model

An object model of the domain that incorporates both behavior and data.

## How it Works
- Creates a network of interconnected objects, where each object represents some meaningful individual part of the business (e.g., a specific Contract, an Invoice).
- Business logic is distributed among these objects.
- Uses OO techniques like inheritance, strategies, and state patterns to handle complexity.

## When to Use
- Complex and ever-changing business logic.
- When you want to leverage OO benefits for long-term maintainability.

## Costs
- **Complexity**: High learning curve for developers new to rich object models.
- **Data Mapping**: Requires a sophisticated mapping layer (e.g., [[Data_Mapper]]) to sync objects with relational databases.

## Comparison
- vs. [[Transaction_Script]]: Better for complex logic; handles duplication through OO abstractions.
- vs. [[Table_Module]]: Domain Model has an instance for *each* record (e.g., an object for *each* Contract), whereas Table Module has one object for *all* records in a table.

## Source
- [[PEAA_Book]] (Chapter 2, Page 126)
