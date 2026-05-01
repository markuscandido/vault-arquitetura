---
title: Putting It All Together (Synthesis)
tags: [architecture, strategy]
status: detailed
date: 2026-05-01
---

# Putting It All Together (Synthesis)

This chapter provides a framework for choosing the right patterns for an application based on its specific needs.

## Choosing Domain Logic Patterns
The choice depends on the complexity of the business logic:
- **[[Transaction_Script]]**: Best for simple logic. Low overhead, easy to understand.
- **[[Table_Module]]**: Good middle ground, especially if working with [[RecordSet]]-heavy platforms (.NET).
- **[[Domain_Model]]**: Best for complex, evolving logic. High learning curve but scales well in maintainability.

## Choosing Data Mapping Patterns
The mapping pattern is often dictated by the chosen domain logic:
- **[[Transaction_Script]]** -> [[Table_Data_Gateway]] or [[Row_Data_Gateway]].
- **[[Table_Module]]** -> [[Table_Data_Gateway]].
- **Simple [[Domain_Model]]** -> [[Active_Record]].
- **Rich [[Domain_Model]]** -> [[Data_Mapper]].

## Architecting the Presentation
- Use **[[MVC]]** as the foundation.
- For web apps, decide between **Template View** (simple, script-like) or **Transform View** (more formal, good for multiple outputs).
- Use a **Front Controller** to centralize common logic (security, logging).

## Concurrency Strategy
- Default to **[[Optimistic_Offline_Lock]]** for better scalability and simplicity.
- Use **[[Pessimistic_Offline_Lock]]** only when the cost of a collision (lost work) is unacceptably high for the user.

## Summary Table
| Logic Complexity | Domain Pattern | Mapping Pattern |
| :--- | :--- | :--- |
| Low | [[Transaction_Script]] | Data Gateway |
| Medium | [[Table_Module]] | [[Table_Data_Gateway]] |
| High | [[Domain_Model]] | [[Data_Mapper]] |

## Source
- [[PEAA_Book]] (Chapter 8)
