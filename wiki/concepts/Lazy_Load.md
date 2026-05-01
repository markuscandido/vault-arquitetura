---
title: Lazy Load
tags: [pattern, data-source, behavioral]
status: detailed
date: 2026-05-01
---

# Lazy Load

An object that doesn't contain all of the data you need but knows how to get it.

## Why is it Needed?
When you load an object from the database, you often load its associated objects. If you have a complex web of objects, loading one could trigger loading the entire database. Lazy Load interrupts this process by loading only the "shallow" object and retrieving the rest only when accessed.

## Implementation Varieties
1. **Lazy Initialization**: Every access to a field checks if it's null and loads it if it is.
2. **Virtual Proxy**: An object that looks like the real object but only loads the real object when a method is called.
3. **Value Holder**: A generic object that wraps the real data and loads it on demand.
4. **Ghost**: An object that is partially loaded (usually just its ID) and loads the rest of its fields the first time any field is accessed.

## Downsides
- **N+1 Selects Problem**: If you lazily load objects in a loop, you can end up with many small database queries instead of one join.

## Source
- [[PEAA_Book]] (Chapter 11, Page 200)
