---
title: Learning Engineering Knowledge Base
date: 2026-08-30
tags:
  - index
  - documentation
---

# 📚 Knowledge Base & System Index

Welcome to the engineering documentation hub. Select a topic below or use `Ctrl + K` (or `Cmd + K`) to search across all notes.

---

## 🛠️ System Guides & Reference

* 🚀 **[[quartz-cheat-sheet|Quartz Complete Cheat Sheet & Reference]]**  
  *Open this guide to learn all syntax rules, callout formats, and Markdown features.*

---

## 🔗 How Double Bracket Links Work (`[[ ]]`)

Quartz uses **WikiLinks** (double square brackets) to build internal links and populate the **Graph View**.

| Syntax | Example | What It Does |
| :--- | :--- | :--- |
| `[[filename]]` | `[[quartz-cheat-sheet]]` | Links directly to `content/quartz-cheat-sheet.md`. |
| `[[filename|Custom Label]]` | `[[quartz-cheat-sheet|Open Cheat Sheet]]` | Links to `quartz-cheat-sheet.md` but displays **Open Cheat Sheet** on the page. |
| `[[filename#section]]` | `[[quartz-cheat-sheet#-1-internal--external-linking|Linking Section]]` | Jumps directly to a specific heading inside that file. |

---

> [!tip] Adding New Notes
> 1. Create any `.md` file inside the `content/` directory (e.g., `content/kafka-notes.md`).
> 2. Add `[[kafka-notes]]` anywhere on this homepage to link it automatically!
