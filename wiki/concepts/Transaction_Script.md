---
title: Transaction Script
tags: [pattern, domain-logic]
status: detailed
date: 2026-05-01
---

# Transaction Script

Organizes business logic by procedures where each procedure handles a single request from the presentation.

## How it Works
- Each logic piece is a procedure (script) that performs all the steps required for a specific business transaction.
- It interacts directly with the database or through a simple [[Data_Gateway]].
- Data is passed around in simple structures (DTOs, RecordSets).

## When to Use
- Simple applications with straightforward business logic.
- When the logic doesn't justify the overhead of a complex object model.

## Comparison
- vs. [[Domain_Model]]: Much simpler to implement but harder to maintain as logic complexity grows due to duplication and lack of OO structure.
- vs. [[Table_Module]]: Less structured than Table Module, as logic is tied to actions rather than tables.

## Source
- [[PEAA_Book]] (Chapter 2, Page 120)
