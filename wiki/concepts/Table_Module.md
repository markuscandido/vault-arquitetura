---
title: Table Module
tags: [pattern, domain-logic]
status: detailed
date: 2026-05-01
---

# Table Module

A single instance that handles the business logic for all rows in a database table or view.

## How it Works
- Unlike [[Domain_Model]], which has one object per record, Table Module has one object per *table*.
- It typically works with a [[RecordSet]] (a data structure holding the results of a query).
- Clients pass a record ID to the Table Module's methods to act on specific data.

## Benefits
- **Integration**: Fits perfectly with platforms that emphasize RecordSets (like .NET or older COM-based systems).
- **Structure**: More organized than [[Transaction_Script]] as logic is grouped by entity/table.

## Downsides
- Cannot use fine-grained OO techniques like polymorphism for individual records.

## Source
- [[PEAA_Book]] (Chapter 2, Page 134)
