---
title: Remote Facade
tags: [pattern, distribution]
status: detailed
date: 2026-05-01
---

# Remote Facade

Provides a coarse-grained facade over a web of fine-grained objects to improve efficiency over a network.

## How it Works
If you have a [[Domain_Model]] with many small objects and associations, you don't want remote clients to interact with them directly. Instead, you create a **Remote Facade** that offers a few high-level methods.
- The Facade translates a single coarse-grained call into many fine-grained calls to the local domain objects.
- It often uses [[DTO|DTOs]] to return data to the client.

## Core Principle
"Don't distribute your objects" (The First Law of Distribution). If you MUST distribute, use a Remote Facade to bridge the gap between fine-grained domain logic and coarse-grained remote communication.

## Source
- [[PEAA_Book]] (Chapter 15, Page 368)
