---
title: Concurrency
tags: [concept, infrastructure]
status: detailed
date: 2026-05-01
---

# Concurrency

Concurrency occurs when multiple users or processes attempt to access and modify the same data simultaneously.

## Problems
- **Lost Updates**: User A and User B read the same record; User A saves, then User B saves, overwriting User A's changes.
- **Inconsistent Reads**: Reading data that is in the middle of being changed by another process.

## ACID Properties
Transactions must be Atomic, Consistent, Isolated, and Durable.

## Offline Concurrency
In enterprise apps, a business transaction (e.g., a multi-screen checkout) often spans multiple system transactions (HTTP requests). Since database transactions shouldn't stay open that long, we use **Offline Locking**:
- [[Optimistic_Offline_Lock]]: Assumes collisions are rare. Checks if data changed before saving.
- [[Pessimistic_Offline_Lock]]: Assumes collisions are likely. Prevents others from accessing data while you have it.

## Source
- [[PEAA_Book]] (Chapter 5)
