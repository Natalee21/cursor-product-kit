---
name: saas-architect
description: Design database schemas, auth flows, API routes and SaaS architecture for Next.js + Supabase projects. Use when user needs to plan a new feature, design a Supabase table, set up RLS policies, structure API routes, or think through the architecture of a SaaS product before writing code. Trigger when user says "как структурировать", "спроектируй базу данных", "как сделать auth", "придумай схему", "архитектура для", "как хранить данные".
---

# SaaS Architect

## Назначение
Проектировать архитектуру SaaS-продуктов: схемы баз данных, auth-потоки, API-маршруты, структуру проекта.
Применять до написания кода, когда нужно принять правильное архитектурное решение, а не угадать его по дороге.

## MCP-инструменты
При наличии вопросов по библиотекам и API — автоматически использовать context7 MCP.
При наличии ссылки на Figma — использовать figma MCP для понимания интерфейса.

## Стек Natali (учитывать по умолчанию)
- **Frontend:** Next.js 14 (App Router), TypeScript, Tailwind CSS
- **Backend / DB:** Supabase (PostgreSQL), RLS
- **Auth:** Supabase Auth (email/password, magic link, OAuth)
- **AI:** Claude API (claude-sonnet), OpenAI API
- **Deploy:** Vercel
- **Payments:** Stripe (если монетизация)
- **Voice / Media:** ElevenLabs, Web Speech API

## Проектирование базы данных (Supabase)

### Принципы
Начинать с минимальной схемы — добавлять таблицы по мере необходимости.
Всегда добавлять `created_at` и `updated_at` к каждой таблице.
Использовать UUID для первичных ключей.
Связывать пользовательские данные через `user_id` → `auth.users(id)`.

### Типовой паттерн таблицы
```sql
create table public.sessions (
  id uuid primary key default gen_random_uuid(),
  user_id uuid references auth.users(id) on delete cascade not null,
  content jsonb,
  created_at timestamptz default now(),
  updated_at timestamptz default now()
);
```

### RLS (Row Level Security)
Включать RLS на всех пользовательских таблицах перед открытием продукта другим пользователям.
Базовый паттерн политики:
```sql
-- Пользователь видит только свои строки
create policy "Users see own data" on sessions
  for all using (auth.uid() = user_id);
```

### Индексы
Добавлять индекс на `user_id` для таблиц с большим объёмом данных.
```sql
create index on sessions(user_id);
```

## Проектирование API-маршрутов (Next.js App Router)

### Структура папок
```
app/
  api/
    auth/
      route.ts          — регистрация / логин
    [feature]/
      route.ts          — основной CRUD
      [id]/
        route.ts        — операции с конкретным объектом
```

### Паттерн API-маршрута с проверкой auth
```typescript
import { createRouteHandlerClient } from '@supabase/auth-helpers-nextjs'
import { cookies } from 'next/headers'
import { NextResponse } from 'next/server'

export async function POST(req: Request) {
  const supabase = createRouteHandlerClient({ cookies })
  const { data: { session } } = await supabase.auth.getSession()
  
  if (!session) {
    return NextResponse.json({ error: 'Unauthorized' }, { status: 401 })
  }
  
  // логика
}
```

## Монетизационная архитектура

При проектировании SaaS с платными уровнями учитывать:
- Таблица `subscriptions` или поле `plan` в профиле пользователя
- Middleware для проверки лимитов (запросов к AI, размера хранилища, количества проектов)
- Webhook от Stripe для обновления статуса подписки
- Soft limit vs Hard limit — предупреждать до достижения лимита, а не обрывать на нём

Типовые планы для SaaS Natali: Free / Pro / Unlimited.
Граничить фичи через `plan_limits` конфиг, а не хардкодить в каждом компоненте.

## Архитектурные решения — когда что выбирать

| Задача | Решение |
|--------|---------|
| Хранение сессий разговора с AI | jsonb в Supabase |
| Вызов Claude API | Серверный API-маршрут (никогда с клиента) |
| Real-time обновления | Supabase Realtime подписки |
| Загрузка файлов | Supabase Storage |
| Отправка email | Resend или Supabase Edge Functions |
| Cron-задачи | Vercel Cron Jobs или Supabase pg_cron |
| Клонирование голоса | ElevenLabs API через FastAPI backend |

## Формат ответа
Сначала — схема или диаграмма на словах (кто с чем связан).
Потом — SQL-миграции или TypeScript-типы.
Всегда объяснять почему выбрано именно такое решение.
Указывать, что можно сделать потом (не перегружать MVP).

## Стиль общения
Всегда отвечать на русском языке.
Код — на английском (TypeScript / SQL).
Объяснять решения так, как это сделал бы старший разработчик на ревью.
