---
title: Quartz Syntax & Reference Guide
date: 2026-08-30
tags:
  - quartz
  - documentation
  - markdown
---

# 🚀 Quartz Cheat Sheet & Reference

This page serves as a live guide for all core Quartz features and Markdown formatting options.

---

## 🔗 1. Internal Links & Graph Connectivity

Quartz uses double brackets `[[ ]]` to automatically connect notes and populate the interactive **Graph View**.

* **Standard Note Link:** [[index]] (Links to `index.md`)
* **Aliased Link:** [[index|Go to Homepage]] (Displays custom text instead of filename)
* **Sub-Header Link:** [[index#welcome|Jump to Welcome Section]] (Links to a specific heading inside a page)

---

## 💡 2. Styled Callout Boxes

Create styled visual highlight blocks using standard blockquote syntax:

> [!note] General Note
> Use this to highlight standard tips, context, or reminders.

> [!tip] Pro Tip
> Press `Ctrl + K` (or `Cmd + K`) on any page to open the global instant search bar.

> [!warning] Production Alert
> Always commit your files to the `v5` branch inside the `/content` directory to trigger auto-deployment.

> [!info] Information
> Folders created inside `/content` automatically turn into expandable categories in the sidebar.

---

## 💻 3. Code Blocks & Syntax Highlighting

Quartz automatically applies theme styling, line numbers, and a copy button to code blocks:

```java
package com.example.notes;

public class QuartzDemo {
    public static void main(String[] args) {
        System.out.println("Quartz automatically formats this code block!");
    }
}
