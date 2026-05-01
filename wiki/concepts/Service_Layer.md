---
title: Service Layer
tags: [pattern, domain-logic, architecture]
status: detailed
date: 2026-05-01
---

# Service Layer

Defines an application's boundary with a layer of services that establishes a set of available operations and coordinates the application's response in each operation.

## How it Works
A Service Layer sits on top of the [[Domain_Layer]] (either [[Domain_Model]] or [[Table_Module]]) and acts as a gateway for different types of clients (Web UI, Web Services, CLI).

### Types of Business Logic
1. **Domain Logic**: Pure logic related to the business problem (e.g., how to calculate revenue). This belongs in the Domain Layer.
2. **Application Logic**: Responsibilities related to the application workflow (e.g., notifying administrators, coordinating multiple domain objects). This belongs in the Service Layer.

### Implementation Variants
- **Domain Facade**: A thin layer of facades over a [[Domain_Model]]. The service layer classes don't implement business logic themselves; they just expose the domain model's capabilities.
- **Operation Script**: Service classes implement the application logic directly but delegate domain-specific logic to domain objects.

## Remote vs. Local
While Service Layer interfaces are often coarse-grained and suitable for remote invocation, Fowler advises starting with **local** invocation. Add a [[Remote_Facade]] only if physical distribution is truly necessary.

## Benefits
- **Centralized Logic**: Prevents duplication of application logic across different controllers.
- **Transaction Control**: Provides a natural place to start and commit transactions.
- **Clear Boundary**: Defines exactly what the application can do from a client's perspective.

## When to Use
- When multiple client types access the same logic.
- When responses to use cases involve complex coordination of multiple resources or notifications.
- *Not needed* for very simple applications with only one client type and simple CRUD operations.

## Source
- [[PEAA_Book]] (Chapter 9, Page 141)
