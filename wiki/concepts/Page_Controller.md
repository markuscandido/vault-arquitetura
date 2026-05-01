---
title: Page Controller
tags: [pattern, presentation]
status: detailed
date: 2026-05-01
---

# Page Controller

An object that handles a request for a specific page or action on a Web site.

## How it Works
A Page Controller is the entry point for a specific URL or action. It coordinates the logic needed to satisfy the request and decides which view to display next.

### Responsibilities
1. **Extract Data**: Reads parameters from the HTTP request (GET/POST).
2. **Invoke Logic**: Calls the appropriate objects in the [[Domain_Layer]].
3. **Select View**: Determines which template or page should be used for the response.
4. **Prepare Data**: Places the results from the domain logic into a format the view can easily read (often using a **Helper Object**).

### Implementation Styles
- **Script-based**: Using a servlet, CGI script, or a specific class to handle the logic before transferring control to a view.
- **Server Page-based**: Using an ASP, JSP, or PHP page that contains logic at the top (often delegated to a "Code Behind" or "Helper" class to avoid "tag soup").

## When to Use
- Best for websites with simple to moderate navigation complexity.
- It is the default, "natural" way to structure web applications where each page has its own handler.

## Comparison
- vs. [[Front_Controller]]: Page Controller has one handler per page/action, while Front Controller has a single handler for the entire application.

## Source
- [[PEAA_Book]] (Chapter 14, Page 318)
