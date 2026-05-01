---
title: Data Mapper
tags: [pattern, data-source]
status: detailed
date: 2026-05-01
---

# Data Mapper

A layer of mappers that moves data between objects and a database while keeping them independent of each other and the mapper itself.

## How it Works
- The [[Domain_Model]] objects are completely unaware of the database.
- The Data Mapper handles the translation between the object network and the relational tables.
- It is responsible for loading objects from the database and saving their state back.

## Benefits
- **Full Decoupling**: Domain and database can evolve independently.
- **Complex Mapping**: Handles cases where the object structure is very different from the database schema (e.g., inheritance, complex associations).

## Downsides
- **Complexity**: It is the most complex mapping pattern to implement.
- Often requires advanced techniques like Identity Map, Unit of Work, and Lazy Load.

## Source
- [[PEAA_Book]] (Chapter 3, Page 170)
