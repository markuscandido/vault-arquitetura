---
title: Special Case
tags: [pattern, base]
status: detailed
date: 2026-05-01
---

# Special Case

A subclass that provides special behavior for particular cases.

## Why is it Needed?
Null pointers are a common cause of errors and "if (obj == null)" checks cause code duplication. A Special Case object provides a "do nothing" or default behavior that matches the expected interface, allowing the rest of the code to treat it like a normal object.

## Examples
- **Null Object**: Instead of returning `null` for a missing customer, return a `NullCustomer` object where `getName()` returns "Occupant" and `getDiscount()` returns 0.
- **Unknown Customer**: For users not yet identified.
- **Infinite**: In numerical systems.

## Benefits
- **Removes Conditionals**: Eliminates repetitive null checks.
- **Polymorphism**: Leverages OO design to handle edge cases cleanly.

## Source
- [[PEAA_Book]] (Chapter 18, Page 462)
