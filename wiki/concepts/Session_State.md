---
title: Session State
tags: [concept, infrastructure]
status: seed
date: 2026-05-01
---

# Session State

Session state is the data maintained by the application about a specific user's interaction over a period of time.

## Strategies for Storage
1. **Client Side**: Storing state in cookies, hidden form fields, or URLs. Good for scalability but has size and security limits.
2. **Server Side (Memory)**: Storing state in the web server's memory (e.g., `HttpSession`). Simple but hard to scale (requires "sticky sessions").
3. **Database/External Store**: Storing state in a database or cache (Redis). Most robust and scalable, but adds latency and complexity.

## Key Principle
Keep session state as small as possible. Prefer stateless designs where practical.

## Source
- [[PEAA_Book]] (Chapter 6)
