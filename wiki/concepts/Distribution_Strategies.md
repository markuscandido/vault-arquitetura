---
title: Distribution Strategies
tags: [architecture, distribution, performance]
status: detailed
date: 2026-05-01
---

# Distribution Strategies

Distribution strategies deal with how to spread an application across multiple processing nodes (servers).

## The First Law of Distributed Objects
**"Don't distribute your objects!"**

Martin Fowler argues that many developers mistakenly distribute objects (e.g., placing different classes on different servers) for performance. In reality, the overhead of remote calls (cross-process or cross-network) is orders of magnitude higher than local calls, often leading to poor performance and complex designs.

## Local vs. Remote Interfaces
- **Local Interfaces**: Can be fine-grained (many small methods). Good for OO design and flexibility.
- **Remote Interfaces**: Must be coarse-grained (few calls that do a lot of work). Designed to minimize the number of network roundtrips.

## Clustering
The preferred alternative to distributing individual objects is **Clustering**.
- Run multiple copies of the *entire* application process on different nodes.
- Each process handles its own requests using local, fine-grained calls.
- Scalability is achieved by adding more nodes to the cluster, not by splitting the object model.

## When to Distribute
Only distribute when necessary:
- **Separating Tiers**: Keeping presentation on a web server and domain logic on an application server (though even this should be scrutinized).
- **Physical Isolation**: When a specific resource (like a legacy system) can only be accessed from a specific node.
- **Vendor Requirements**: When a 3rd party service requires a remote interface.

## Source
- [[PEAA_Book]] (Chapter 7)
