---
title: Quartz Cheat Sheet & Reference Guide
date: 2026-08-30
tags:
  - quartz
  - markdown
  - documentation
---

# 🚀 Quartz Complete Cheat Sheet

This page provides functional examples of all core Quartz features and Markdown elements.

---

## 🔗 1. Internal & External Linking

Quartz uses double brackets `[[ ]]` for internal links, which automatically link pages and update your interactive **Graph View**.

### A. Internal Links (WikiLinks)
* **Link to Homepage:** [[index]]
* **Link with Custom Text:** [[index|Return to Developer Profile]]
* **Link to Specific Heading:** [[index#-professional-experience|Jump to Experience Section]]

### B. External Links & Contact Links
* **Web Link:** [GitHub Profile](https://github.com/Yatharth1515)
* **Direct Email:** [Send Email](mailto:yatharthbaghel@gmail.com)
* **Direct Call:** [Call Phone](tel:+918839095297)

---

## 💡 2. Interactive Callout Boxes

Quartz turns blockquotes with special tags into styled highlight boxes:

> [!note] General Note
> Use this to highlight general tips, architectural notes, or reminders.

> [!tip] Keyboard Shortcut
> Press `Ctrl + K` (or `Cmd + K`) anywhere on the site to open instant full-text search.

> [!warning] Branch Deployment
> Ensure all changes are committed to your working deployment branch (e.g., `v5`) inside the `content/` folder.

> [!info] Information
> Subfolders created inside `content/` automatically render as expandable categories in the left sidebar.

> [!success] Build Status
> When GitHub Actions completes the build, changes reflect live within 1–2 minutes.

---

## 💻 3. Code Blocks & Syntax Highlighting

Quartz automatically handles line numbers, code highlighting, and includes a copy button:

```java
package com.example.service;

import org.springframework.stereotype.Service;

@Service
public class IngestionService {
    
    public void processData(String payload) {
        // Quartz formats Java code with custom theme styling
        System.out.println("Processing: " + payload);
    }
}
