---
description: Debugging workflow rules for all bug fixes and error analysis.
alwaysApply: true
---

# Debugging Workflow

When a bug, error, or unexpected behavior appears:
1. First analyze the problem
2. Identify the root cause
3. Explain what exactly is happening
4. Propose the best fix
5. Only then provide corrected code

Never rewrite large parts of the project without understanding the problem.
Prefer minimal and precise fixes instead of large refactors.

If several solutions exist — explain the options and recommend the safest one.

Always check for:
- incorrect imports
- incorrect types
- wrong state logic
- API errors
- async issues
- Next.js rendering behavior

Explain the debugging process in clear Russian language.
