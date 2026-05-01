---
title: Data Transfer Object (DTO)
tags: [pattern, distribution]
status: detailed
date: 2026-05-01
---

# Data Transfer Object (DTO)

An object that carries data between processes in order to reduce the number of method calls.

## Why is it Needed?
In a distributed system, remote calls are expensive. If you use a fine-grained interface (many small getters/setters), you will suffer from extreme latency. A DTO allows you to gather all the data needed for a specific task and send it in a single remote call.

## Characteristics
- **Data Only**: DTOs should contain only data fields and simple getters/setters. They have **no business logic**.
- **Serializable**: DTOs must be easily converted to a format (like XML, JSON, or Binary) that can be sent over the network.
- **Flat Structure**: Often flattened to simplify the data being transferred, though they can contain nested DTOs for complex structures.

## Implementation
- Often generated automatically from domain objects or database schemas.
- In Java, they often implement `Serializable`. In .NET, they are often simple `structs` or `classes`.

## Source
- [[PEAA_Book]] (Chapter 15, Page 380)
