---
title: Active Record
tags: [pattern, data-source, domain-logic]
status: detailed
date: 2026-05-01
---

# Active Record

An object that wraps a row in a database table or view, encapsulates the database access, and adds domain logic on that data.

## How it Works
- Combines data access (like [[Row_Data_Gateway]]) and business logic in the same class.
- The class structure closely mirrors the database table structure.
- Methods typically include CRUD operations and domain calculations/validations.

## When to Use
- Simple to moderately complex domain logic.
- When there is a 1-to-1 correspondence between domain objects and database tables.
- It is the simplest approach for combining data and behavior.

## Downsides
- Couples the [[Domain_Model|Domain Layer]] directly to the [[Data_Source_Layer]] and the database schema.

## Source
- [[PEAA_Book]] (Chapter 3, Page 165)
