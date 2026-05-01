---
title: Front Controller
tags: [pattern, presentation]
status: detailed
date: 2026-05-01
---

# Front Controller

A single handler that centralizes all requests for a Web site.

## How it Works
Instead of having a separate [[Page_Controller]] for every URL, a Front Controller uses a single object to receive all incoming requests. This controller then delegates the work to "command" or "action" objects.

### Responsibilities
1. **Centralized Logic**: Handles common tasks like security, internationalization, logging, and routing in one place.
2. **Routing**: Decodes the URL and decides which action/command object should handle the business logic.
3. **View Management**: Decides which view to display based on the result of the action.

### Implementation
- Usually implemented as a **Servlet** (Java) or a **global script** (PHP/ASP) that acts as the entry point for the entire application.
- Uses a **Command Map** or a dynamic loading mechanism to find the right action class for a request.

## Benefits
- **Avoids Duplication**: Common request-handling logic is not repeated across multiple pages.
- **Easily Extensible**: Adding new pages or actions only requires adding a new command class and updating the mapping.

## Source
- [[PEAA_Book]] (Chapter 14, Page 328)
