---
description: Backend and API development rules for all server-side code.
alwaysApply: true
---

# Backend Architecture

## Stack
- Next.js API Routes and Server Actions
- Node.js
- REST and webhook integrations
- Telegram Bot API
- AI service integrations (OpenAI, Anthropic and others)
- n8n automation workflows

## Core principles
- Keep API routes simple, focused, and easy to maintain
- Validate incoming data before processing
- Handle errors explicitly — never swallow exceptions silently
- Return clear and consistent response structures
- Never expose sensitive data or API keys in responses

## Telegram bots
- Keep bot logic modular — separate command handlers from business logic
- Handle webhook errors gracefully
- Always respond to Telegram even if processing fails, to avoid retries

## AI integrations
- Keep prompts in separate files or constants, not hardcoded inline
- Handle API errors and rate limits explicitly
- Log inputs and outputs when debugging, but never log sensitive user data

## n8n automation
- Treat n8n workflows as external integrations
- Always validate data coming from n8n webhooks
- Keep webhook endpoints simple and focused on one task

## Editing behavior
- Never change working integration logic unless explicitly asked
- Prefer minimal and precise edits
- If a refactor would improve reliability, suggest it first

## Response style
- Explain backend decisions in clear Russian language
- Be specific about why a particular approach is safer or more reliable
