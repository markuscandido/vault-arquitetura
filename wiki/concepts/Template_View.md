---
title: Template View
tags: [pattern, presentation]
status: detailed
date: 2026-05-01
---

# Template View

Renders information into HTML by embedding markers in an HTML page.

## How it Works
A Template View starts with the desired output (HTML) and allows you to "plug in" dynamic data from the model.
- **Scriptlets**: Small pieces of code embedded directly in the HTML (e.g., `<?php echo $name; ?>`).
- **Custom Tags**: Using specific markers that are replaced by the server (e.g., `<jsp:getProperty ... />`).

## Benefits
- **WYSIWYG Friendly**: Designers can often open the template in an editor and see roughly how it will look.
- **Natural for Web**: Most developers are comfortable with this "HTML-first" approach.

## Downsides
- **Tag Soup**: Logic can easily leak into the view, making it messy and hard to test.

## Source
- [[PEAA_Book]] (Chapter 14, Page 333)
