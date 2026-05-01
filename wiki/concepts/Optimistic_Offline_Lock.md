---
title: Optimistic Offline Lock
tags: [pattern, concurrency]
status: detailed
date: 2026-05-01
---

# Optimistic Offline Lock

Prevents conflicts between concurrent business transactions by detecting a conflict and rolling back the transaction.

## How it Works
- Each record has a version number or timestamp.
- When you read data, you keep the version.
- When you save data, you check if the version in the database is the same as the one you have.
- `UPDATE table SET ... WHERE id = @id AND version = @old_version`.
- If 0 rows are updated, someone else changed the data, and you must handle the conflict (usually by telling the user).

## Benefits
- High concurrency (no long-term locks).
- Easy to implement.

## Downsides
- User only finds out about the conflict at the very end of their work.

## Source
- [[PEAA_Book]] (Chapter 5, Page 392)
