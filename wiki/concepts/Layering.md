---
title: Layering
tags: [architecture, concept]
status: detailed
date: 2026-05-01
---

# Layering

Layering is the most common technique for decomposing complex software systems. It organizes subsystems like a "layer cake," where each layer rests on a lower one.

## Core Principles
- **Dependency Direction**: Higher layers use services from lower layers, but lower layers are unaware of higher layers.
- **Encapsulation**: Layers typically hide their sub-layers (e.g., Layer 4 uses Layer 3, which uses Layer 2, but Layer 4 is unaware of Layer 2).

## Benefits
- **Comprehensibility**: You can understand a single layer without knowing the whole system.
- **Substitutability**: Layers can be replaced with alternative implementations of the same services.
- **Minimized Dependencies**: Changes in lower layers (e.g., physical hardware) don't necessarily affect higher layers.
- **Standardization**: Layers provide natural points for defining standards (e.g., TCP/IP).
- **Reusability**: Once built, a lower layer can be used by multiple higher-level services.

## Downsides
- **Cascade Changes**: Some changes (e.g., adding a field) must ripple through all layers.
- **Performance Overhead**: Data transformation between layers can slow down the system, though encapsulation benefits often compensate.

## The Three Principal Layers
In Enterprise Applications, the standard decomposition is:
1. **[[Presentation_Layer]]**: Interface with the user (Web, GUI, CLI).
2. **[[Domain_Model|Domain Layer]]**: Business logic, calculations, and rules.
3. **[[Data_Source_Layer]]**: Interaction with databases, messaging systems, and other APIs.

## Source
- [[PEAA_Book]] (Chapter 1)
