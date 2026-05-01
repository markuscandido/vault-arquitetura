---
title: Identity Field
tags: [pattern, data-source, structural]
status: detailed
date: 2026-05-01
---

# Identity Field

Saves a database ID field in an object to maintain identity between an in-memory object and a database row.

## How it Works
The Identity Field is a simple field (usually a `long` or `UUID`) in the domain object that stores the primary key of the corresponding database row.

## Key Considerations
- **Meaningless vs. Meaningful Keys**: Fowler strongly prefers meaningless keys (surrogate keys) as they are more stable. Meaningful keys (like Social Security Numbers) can change or be reused, causing mapping nightmares.
- **Key Generation**:
    - **Database Managed**: Using `AUTO_INCREMENT` or `IDENTITY`. Easy but makes it hard to know the ID before saving.
    - **Application Managed**: Generating a UUID or using a **Key Table** (a special table that tracks the next available ID). Allows the object to have an ID before it's even saved.

## Source
- [[PEAA_Book]] (Chapter 12, Page 215)
