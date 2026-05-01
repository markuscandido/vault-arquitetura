---
title: Foreign Key Mapping
tags: [pattern, data-source, structural]
status: detailed
date: 2026-05-01
---

# Foreign Key Mapping

Maps an association between objects to a foreign key reference between tables.

## How it Works
In an object model, objects refer to each other directly. In a relational database, rows refer to each other using foreign keys.

### 1-to-1 and Many-to-1
- For a simple reference from Object A to Object B, you store the ID of B in the table for A.
- Example: An `Album` has one `Artist`. The `Albums` table has an `artist_id` column.

### 1-to-Many
- Because relational tables cannot store collections, you must "invert" the reference.
- Example: An `Album` has many `Tracks`. Instead of the `Albums` table having a list of tracks, the `Tracks` table has an `album_id` column.

## Handling Updates in Collections
When a collection changes (items added or removed), you have three main strategies:
1. **Delete and Insert**: Delete all existing related rows and insert the current collection state. Simple but only works if the children are [[Dependent_Mapping|Dependent Mappings]].
2. **Back Pointer**: Ensure the child object has a reference to the parent. This turns the relationship into a simple Many-to-1 mapping.
3. **Diffing**: Compare the current in-memory collection with the database state and issue only the necessary INSERT, UPDATE, or DELETE commands.

## Source
- [[PEAA_Book]] (Chapter 12, Page 233)
