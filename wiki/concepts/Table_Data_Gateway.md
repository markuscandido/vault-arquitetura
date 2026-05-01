---
title: Table Data Gateway
tags: [pattern, data-source]
status: detailed
date: 2026-05-01
---

# Table Data Gateway

An object that acts as a Gateway to a database table. One instance handles all the rows in the table.

## How it Works
- Provides methods for CRUD (Create, Read, Update, Delete) operations on a specific table.
- Every SQL query for that table is encapsulated within this object.
- Typically returns a [[RecordSet]] or simple data structures.

## When to Use
- When using [[Transaction_Script]] or [[Table_Module]] for domain logic.
- It provides a simple way to organize SQL code.

## Source
- [[PEAA_Book]] (Chapter 3, Page 151)
