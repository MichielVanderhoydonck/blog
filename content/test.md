+++
title = "Test Page"
date = "2024-08-01"
description = "A test"
[taxonomies]
tags = ["markdown", "test"]
+++

# Testing out

This post mostly serves testing.

# It was a beautiful day!

# Let's tryout Mermaid

{% mermaid() %}
graph TD
    A[Enter Chart Definition] --> B(Preview)
    B --> C{decide}
    C --> D[Keep]
    C --> E[Edit Definition]
    E --> B
    D --> F[Save Image and Code]
    F --> B
{% end %}