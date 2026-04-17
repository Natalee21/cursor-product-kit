---
description: TypeScript engineering rules for all code generation.
globs: "**/*.ts,**/*.tsx"
alwaysApply: false
---

# TypeScript Engineering

Always use TypeScript. Avoid using `any` type.
Prefer precise and readable types.

When the exact type is unknown — use `unknown` and narrow the type.
Prefer type safety over quick fixes.
Type function parameters and return values when helpful.
Reuse existing types from the codebase when possible.

Avoid unnecessary complex generics.
Prefer readable types instead of clever but hard-to-understand typings.
Keep types close to the logic they describe unless they are shared across multiple files.
Do not invent abstractions if they are not needed.

When editing existing code — respect existing type patterns used in the project.
If types are missing and improving them will prevent bugs — suggest improvements before implementing changes.

Explain TypeScript decisions in clear Russian language.
