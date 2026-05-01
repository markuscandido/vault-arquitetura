---
title: Money
tags: [pattern, base]
status: detailed
date: 2026-05-01
---

# Money

Represents a monetary value, encapsulating the amount and the currency.

## Why is it Needed?
Using simple floating-point numbers (like `double` or `float`) for money is a major anti-pattern due to rounding errors. A `Money` object ensures that calculations are precise and that different currencies are not accidentally mixed.

## Key Features
- **Amount & Currency**: Stores both the numerical value (usually as a `long` of the smallest unit, like cents) and the currency type.
- **Arithmetic Operations**: Provides methods for addition, subtraction, and comparison that check for currency compatibility.
- **Rounding & Allocation**: Encapsulates logic for rounding and allocating money (e.g., splitting $0.05 into two accounts 70/30 without losing a cent).
- **Format & Parsing**: Handles display and input in a currency-aware way.

## Implementation Tips
- Use **[[Value_Object]]** semantics (immutability).
- Provide an `allocate` method to handle the "Foemmel's Conundrum" (allocating cents across multiple targets).

## Source
- [[PEAA_Book]] (Chapter 18, Page 455)
