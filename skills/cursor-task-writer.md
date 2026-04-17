---
name: cursor-task-writer
description: Write structured .md task documents for the Cursor agent. Use when user wants to create a task for Cursor, plan a coding session, or prepare instructions for AI-assisted development. Trigger when user says "напиши задачу для Cursor", "создай .md для агента", "подготовь таск", "оформи задание для Cursor" or describes what they want to build and needs it formatted for the Cursor agent.
---

# Cursor Task Writer

## Назначение
Создавать структурированные `.md`-документы для отправки в Cursor-агент.
Применять каждый раз, когда пользователь хочет что-то запрограммировать через Cursor и нужно правильно оформить задание.

## Обязательная структура документа

Каждый документ состоит из двух блоков:

### Блок 1 — Контекст для агента (Context Block)
Рассказывает агенту, кто мы, какой проект, какой стек и какие правила.
Размещается в начале файла.

```markdown
# Context

## Project
[Название проекта и краткое описание — 1-2 предложения]

## Stack
- Frontend: Next.js 14 (App Router), TypeScript, Tailwind CSS
- Backend: [Supabase / FastAPI / etc.]
- Deploy: Vercel
- [другие технологии проекта]

## Design reference
Always follow @DESIGN.md for all visual decisions — colors, fonts, spacing, components.
DESIGN.md is the single source of truth for the visual system.

## Rules
- Write complete files without abbreviations or truncation
- One file per task — complete the file fully before moving to next
- Do not break existing code or structure
- Prefer minimal, precise edits over large rewrites
- If a big change is needed — suggest it first, do not apply automatically
- Always respond in Russian

## Existing files relevant to this task
[Перечислить файлы, на которые агент должен обратить внимание, например:]
- @app/api/talk/route.ts
- @components/TalkModule.tsx
- @lib/supabase.ts
```

### Блок 2 — Задание для агента (Task Block)
Конкретное, пошаговое задание. Описывается что нужно сделать, а не как именно.

```markdown
# Task: [Название задачи]

## Goal
[Одно предложение — чего мы хотим достичь]

## What to implement
1. [Первый шаг — конкретное действие]
2. [Второй шаг]
3. [Третий шаг]

## Files to create or modify
- `path/to/file.ts` — [что именно изменить]
- `path/to/new-file.tsx` — [что создать]

## Acceptance criteria
- [ ] [Конкретный результат, который можно проверить]
- [ ] [Ещё один результат]

## Do NOT touch
- [Файлы или логика, которые трогать нельзя]
```

## Правила написания заданий

**Полный код, никаких сокращений.** Никогда не писать `// ... rest of the code` или `// existing code here`. Агент должен выдавать файл целиком.

**Один файл за раз.** Если задача затрагивает несколько файлов — разбивать на отдельные подзадачи или чётко указывать порядок.

**DESIGN.md всегда первым правилом.** Каждый документ, связанный с UI, должен начинаться с ссылки на DESIGN.md как на главный источник дизайн-системы.

**Конкретные acceptance criteria.** Не «кнопка должна работать», а «при нажатии на кнопку Send открывается модальное окно с подтверждением».

**Указывать что нельзя трогать.** Это защищает от случайного сноса рабочего кода.

## Когда нужно несколько задач
Если пользователь описывает большую фичу — сначала разбить на атомарные таски (Task 01, Task 02...), а потом оформить каждый отдельно.
Каждый таск должен быть завершённым и работающим самостоятельно, чтобы можно было сделать git commit после каждого.

## Напоминание о git
После каждого успешно выполненного таска — напоминать пользователю сделать коммит:
```bash
git add .
git commit -m "feat: [описание что сделано]"
```
Это защищает от потери изменений при следующей итерации с Cursor.

## Стиль общения
Всегда отвечать на русском языке.
Задание для агента писать на английском (Cursor лучше понимает технические инструкции на английском).
Пояснения для пользователя — на русском.
