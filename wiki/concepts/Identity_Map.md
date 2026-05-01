---
title: Identity Map
tags: [pattern, data-source, behavioral]
status: detailed
date: 2026-05-01
---

# Identity Map

Ensures that each object gets loaded only once by keeping every loaded object in a map. Looks up objects using the map when referring to them.

## How it Works
When you load data from a database, the Identity Map checks if it already has an object with that ID.
- If **yes**: It returns the existing instance.
- If **no**: It creates a new instance, puts it in the map, and returns it.

## Why is it Needed?
In a relational database, you identify a row by its primary key. In an OO environment, you identify an object by its reference or identity. Without an Identity Map, you might load the same database row into two different objects in memory. If you modify both, which one should be saved? The Identity Map prevents this "duplicate object" problem.

## Scope
Identity Maps are usually scoped to a single **Request** or **Transaction**. Keeping them globally across multiple sessions is dangerous as it leads to stale data and memory leaks.

## Source
- [[PEAA_Book]] (Chapter 11, Page 196)
