---
title: Quartz Syntax & Reference Guide
date: 2026-08-30
tags:
  - quartz
  - documentation
  - markdown
---

# 🚀 Quartz Cheat Sheet & Reference

This page serves as a live guide for all core Quartz features, Markdown formatting options, and linking best practices.

---

## 🔗 1. Complete Guide to Linking in Quartz

Quartz supports standard WikiLinks (`[[...]]`), Markdown links (`[...](...)`), file attachments, and cross-references. All internal WikiLinks automatically populate your interactive **Graph View**.

### A. Internal Note Links (WikiLinks)
* **Standard Page Link:**  
  `[[index]]` $\rightarrow$ Links to `content/index.md`
* **Custom Display Text (Aliased Link):**  
  `[[index|Back to Homepage]]` $\rightarrow$ Displays "Back to Homepage" instead of the filename.
* **Folder / Subpath Link:**  
  `[[backend/spring-boot]]` $\rightarrow$ Links to a nested file at `content/backend/spring-boot.md`.
* **Section / Heading Link:**  
  `[[profile#tech-stack|View Tech Stack Section]]` $\rightarrow$ Jumps directly to the `# Tech Stack` heading inside `profile.md`.

### B. Media & File Attachments (Images & PDFs)
Store files inside your `content/` folder (e.g., in a `content/assets/` folder):

* **Embed an Image:**  
  `![[assets/architecture-diagram.png]]` or `![Architecture Diagram](assets/architecture-diagram.png)`
* **Image with Custom Width:**  
  `![[assets/architecture-diagram.png|500]]`
* **Link to PDF File (Download):**  
  `[Download PDF Resume](assets/Yatharth_Resume.pdf)`
* **Embed PDF Viewer Directly:**  
  `<iframe src="assets/Yatharth_Resume.pdf" width="100%" height="600px"></iframe>`

### C. External Links (Web Sites)
* **Standard Web Link:**  
  `[LinkedIn Profile](https://linkedin.com/in/yatharth-singh-baghel-)`
* **Mail & Phone Links:**  
  `[Send Email](mailto:yatharthbaghel@gmail.com)` | `[Call Me](tel:+918839095297)`

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
