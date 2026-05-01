---
title: Pessimistic Offline Lock
tags: [pattern, concurrency]
status: detailed
date: 2026-05-01
---

# Pessimistic Offline Lock

Prevents conflicts between concurrent business transactions by allowing only one person to access data at a time.

## How it Works
- When a user starts editing, the application acquires a lock for that data in a central lock manager (or a lock table in the DB).
- Other users attempting to edit the same data are blocked or given an error immediately.
- The lock is released when the business transaction is finished (saved or cancelled).

## Benefits
- Users find out early if they can't edit data.
- Avoids lost work at the end of a transaction.

## Downsides
- Lower concurrency.
- Harder to implement (need to handle lock timeouts and abandoned sessions).

## Source
- [[PEAA_Book]] (Chapter 5, Page 401)
