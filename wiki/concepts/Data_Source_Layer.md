---
title: Data Source Layer
tags: [architecture, layer]
status: detailed
date: 2026-05-01
---

# Data Source Layer

The Data Source Layer is responsible for communicating with other systems that carry out tasks on behalf of the application. This typically involves databases, messaging systems, and external APIs.

## Responsibilities
- **Data Persistence**: Saving and retrieving data from long-term storage (relational databases, NoSQL, etc.).
- **Integration**: Interfacing with legacy systems or 3rd party services.
- **Abstraction**: Providing a clean API to the [[Domain_Model|Domain Layer]] so it doesn't have to deal with the specifics of SQL, network protocols, or external data formats.

## Key Mapping Patterns
Depending on the complexity of the domain and the degree of decoupling required, different patterns are used:
- [[Table_Data_Gateway]]: One object for a whole table.
- [[Row_Data_Gateway]]: One object for a single row in a table.
- [[Active_Record]]: An object that wraps a row and includes business logic.
- [[Data_Mapper]]: A layer that moves data between objects and a database while keeping them independent.

## Source
- [[PEAA_Book]] (Chapter 1 & 3)
