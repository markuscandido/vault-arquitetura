---
title: Row Data Gateway
tags: [pattern, data-source]
status: detailed
date: 2026-05-01
---

# Row Data Gateway

An object that acts as a Gateway to a single record in a data source. There is one instance per row.

## How it Works
- The object has one field for each column in the database table.
- It encapsulates the access to the record (e.g., getters/setters) but contains **no business logic**.
- Usually provides an `update()` or `insert()` method to persist its state.

## When to Use
- When you want to separate data access from business logic but still want an object-oriented view of the records.
- Often used with [[Transaction_Script]].

## Source
- [[PEAA_Book]] (Chapter 3, Page 160)
