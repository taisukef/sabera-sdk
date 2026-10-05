---
title: Authoring Documentation
nav_order: 9
---

# Authoring Documentation

[日本語](authoring.md) | [English](authoring_en.md)

This guide explains the documentation site structure, how API pages and code snippets are generated, how to preview the
site locally, and how the CI workflow validates and deploys the site.

API pages are generated from the SDK specification. When adding a public API, update the specification and run the
generation script rather than editing generated pages by hand.

```bash
python3 scripts/gen-api-docs.py
```

To preview the site locally:

```bash
cd docs
bundle exec jekyll serve
```
