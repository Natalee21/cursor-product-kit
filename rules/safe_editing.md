---
description: Safe editing rules for all code modifications.
alwaysApply: true
---

# Safe Editing

When modifying existing code:
- Always preserve the current project structure
- Do not rename files, routes, or folders unless explicitly requested
- Do not refactor large parts of the codebase unless it is necessary to solve the problem
- Prefer minimal and precise edits

When updating a file:
- Change only the parts required to solve the task
- Avoid rewriting unrelated sections of the file

If a large refactor could improve the code — suggest it first instead of applying it automatically.
Respect existing naming conventions, folder structure, and coding style used in the project.
If an edit might affect multiple parts of the system — explain the possible impact before making changes.

Explain the reasoning in clear Russian language.
