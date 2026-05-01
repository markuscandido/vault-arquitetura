---
title: Unit of Work
tags: [pattern, data-source, behavioral]
status: detailed
date: 2026-05-01
---

# Unit of Work

Maintains a list of objects affected by a business transaction and coordinates the writing out of changes and the resolution of concurrency problems.

## How it Works
A Unit of Work keeps track of everything you do during a business transaction that can affect the database. When you're done, the Unit of Work figures out everything that needs to be done to alter the database as a result of your work.

### Key Responsibilities
1. **Change Tracking**: Tracks which objects are new, dirty (modified), or deleted.
2. **Commit Coordination**: When `commit()` is called, it opens a database transaction and executes all necessary INSERT, UPDATE, and DELETE commands.
3. **Consistency**: Ensures that all changes are written or none are, maintaining the ACID properties of the business transaction.

## Benefits
- **Reduces Database Calls**: By batching updates, it minimizes the number of roundtrips to the database.
- **Simplifies Domain Logic**: Developers don't have to remember to call `save()` on every object; the Unit of Work handles it.
- **Concurrency Management**: It's a natural place to implement [[Optimistic_Offline_Lock]] checks.

## Comparison
- Without a Unit of Work, you might update the database every time you change a domain object, leading to many small, inefficient transactions.

## Source
- [[PEAA_Book]] (Chapter 11, Page 187)
