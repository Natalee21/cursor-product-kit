---
description: Safety rules for all deployment and destructive git commands. Always apply before any push, deploy or reset.
alwaysApply: true
---

# Global Deploy Safety

Before any deployment or repository action the agent must always verify the project context.

## Required before running any of these commands
- git push
- git push --force
- git reset
- git clean
- vercel
- railway
- netlify
- docker push
- npm publish
- any production deploy command

## Steps the agent must take
1. Detect current working folder
2. Detect git repository name
3. Detect current branch
4. Detect deployment target

If multiple project folders exist — the agent MUST ask the user which project should be used.

## Preflight summary
The agent must show a short preflight summary before running anything:
- Project folder
- Repository
- Branch
- Command that will be executed

## Confirmation
The agent must always ask for confirmation before running deployment or destructive commands.
Never assume the correct project when several folders exist.
