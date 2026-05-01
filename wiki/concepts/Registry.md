---
title: Registry
tags: [pattern, base]
status: detailed
date: 2026-05-01
---

# Registry

A well-known object that other objects can use to find common objects and services.

## How it Works
A Registry acts as a global point of access to find objects when you don't have a direct reference. It is often implemented using a **Singleton** or static methods.

## Scopes
- **Process Scope**: Data shared by the entire application process.
- **Thread Scope**: Data specific to the current thread (e.g., a database connection).
- **Session Scope**: Data specific to a user session.

## Implementation
- **Static Methods**: Easy to call from anywhere: `Registry.personFinder()`.
- **Interface vs Implementation**: It's best to have the Registry return an interface so the implementation can be swapped (e.g., for testing).

## Warning
A Registry is effectively **Global Data**. Use it as a last resort. Prefer passing dependencies through constructors (Dependency Injection) whenever possible.

## Source
- [[PEAA_Book]] (Chapter 18, Page 448)
