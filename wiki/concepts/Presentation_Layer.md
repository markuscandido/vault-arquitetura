---
title: Presentation Layer
tags: [architecture, layer]
status: detailed
date: 2026-05-01
---

# Presentation Layer

The Presentation Layer is responsible for handling the interaction between the user and the software. This can range from a command-line interface (CLI) to a rich graphical user interface (GUI) or a web-based interface.

## Responsibilities
- **Display Information**: Presenting data from the [[Domain_Model|Domain Layer]] to the user.
- **Capture Input**: Receiving commands and data from the user.
- **Interpret Input**: Translating user actions into calls to the [[Domain_Model|Domain Layer]] or [[Data_Source_Layer]].

## Core Patterns
The most fundamental pattern in presentation is **[[MVC]]**, which separates the internal representation of information from the ways that information is presented to and accepted from the user.

### Web Presentation Patterns
- **Template View**: Rendering information into HTML using markers in a template.
- **Transform View**: Using a transformation (like XSLT) to turn domain data into HTML.
- **Front Controller**: A single handler for all requests to a website.
- **Application Controller**: Managing the flow of a multi-screen application.

## Source
- [[PEAA_Book]] (Chapter 1 & 4)
