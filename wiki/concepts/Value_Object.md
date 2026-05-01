---
title: Value Object
tags: [pattern, base]
status: detailed
date: 2026-05-01
---

# Value Object

A small simple object, like money or a date range, whose equality isn't based on identity.

## How it Works
Two Value Objects are considered "the same" if their values are equal, even if they are different instances in memory.
- Example: Two $5 bills are functionally identical for most business purposes.
- Example: Two `DateRange` objects representing "2026-01-01 to 2026-01-02" are equal.

## Core Rules
1. **Immutability**: Once created, a Value Object should never change. To "change" it, you create a new instance.
2. **Value-based Equality**: Overwrite the `equals()` and `hashCode()` methods to compare fields rather than memory addresses.

## Benefits
- **No Side Effects**: Since they are immutable, you can pass them around without worrying that another part of the system will change their value.
- **Simplifies Logic**: You don't need to track identity for small concepts.

## Source
- [[PEAA_Book]] (Chapter 18, Page 453)
