---
name: ai-bot-builder
description: Design and build AI assistants, chatbots and bots for Telegram, Max (mail.ru) and VKontakte. Use when user wants to create an AI-powered bot, configure a system prompt, design conversation flow, optimize token usage, or integrate Claude/OpenAI API into any messenger. Trigger when user says "сделай бота", "настрой ассистента", "системный промпт", "Telegram-бот", "бот для ВКонтакте", "бот для Max", "AI-помощник", "чат с AI", "интеграция с Claude", "как сэкономить токены", "оптимизация API-запросов", or describes a conversational product they want to build.
---

# AI Bot Builder

## Назначение
Проектировать и создавать AI-ассистентов и чат-боты для мессенджеров — Telegram, Max (mail.ru) и ВКонтакте — от системного промпта до полной интеграции с API.
Применять когда нужно создать разговорный интерфейс, настроить поведение AI, выстроить логику диалога, подключить языковую модель к продукту или оптимизировать расходы на API.

## MCP-инструменты
При вопросах по библиотекам — автоматически использовать context7 MCP.
Никогда не спрашивать разрешения, вызывать молча.

---

## Стек по умолчанию (проекты Natali)

- **Claude API** — claude-sonnet-4-20250514 (основная модель для сложных диалогов)
- **Claude Haiku** — для быстрых и дешёвых задач: перевод, коррекция, классификация
- **OpenAI Whisper** — транскрипция голосовых сообщений
- **Next.js API routes** — серверный слой для всех вызовов к AI
- **Supabase** — хранение истории диалогов, пользователей, контекста сессии
- **grammy** — библиотека для Telegram-ботов на TypeScript
- **vk-io** — библиотека для ботов ВКонтакте на TypeScript
- **Max Bot API** — HTTP webhook, официальный API мессенджера Max

---

## Проектирование системного промпта

### Структура хорошего системного промпта
```
1. Роль и личность      — кто этот ассистент, как его зовут, какой у него характер
2. Контекст             — о чём продукт, для кого создан, в какой ситуации работает
3. Задача               — что именно делает ассистент в каждом диалоге
4. Ограничения          — чего не делает, о чём не говорит, как реагирует на off-topic
5. Формат ответов       — длина, тон, структура, язык, использование эмодзи
6. Few-shot примеры     — 2–3 пары вопрос/ответ, если нужен точный стиль
```

### Принципы написания промпта
Писать конкретно — «отвечай не длиннее 3 предложений» вместо «будь кратким».
Ограничения важнее инструкций: модель лучше выполняет запреты, чем размытые пожелания.
Язык промпта = язык ответов. Если бот отвечает на русском — промпт на русском.
Тестировать промпт на крайних случаях: грубость, off-topic, пустое сообщение, голосовое.

### Пример системного промпта (Wayspeak / Star)
```
You are Star, a friendly English speaking coach inside the Wayspeak app.
Your job is to help the user practice spoken English in a safe, low-pressure environment.

The user is a Russian speaker building confidence in English conversation.
Respond like a patient, encouraging native-speaking friend — not a formal teacher.

Rules:
- Always respond in English, keep answers to 2–4 conversational sentences
- If the user makes a grammar mistake, correct it gently at the end of your reply
- Always end with one follow-up question to keep the conversation going
- Never switch to Russian even if the user writes in Russian
- If the user seems stuck, offer a simple sentence starter like "You could say..."

Tone: warm, playful, supportive.
```

---

## Оптимизация токенов (Token Economy)

Токены — это деньги. Каждый лишний запрос к Sonnet, который мог уйти в Haiku, стоит в 5–10 раз дороже. Экономия проектируется заранее, а не добавляется потом.

### Правило выбора модели

| Задача | Модель | Почему |
|--------|--------|--------|
| Свободный диалог, сложные вопросы | claude-sonnet | Требует рассуждения |
| Перевод на русский | claude-haiku | Детерминированная задача |
| Грамматическая коррекция | claude-haiku | Простая структура вывода |
| Классификация намерения пользователя | claude-haiku | Бинарный или категориальный ответ |
| Краткое резюме сессии | claude-haiku | Шаблонный формат |
| Генерация hints / подсказок | claude-haiku | Короткий предсказуемый вывод |
| Анализ тональности / эмоций | claude-haiku | Классификация |
| Сложный анализ документа | claude-sonnet | Нужно глубокое понимание |

### Объединять задачи в один запрос
Вместо двух отдельных вызовов (перевод + коррекция) делать один:
```typescript
const prompt = `
Analyze this English text written by a language learner: "${userMessage}"

Respond ONLY with valid JSON, no markdown, no explanation:
{
  "has_errors": boolean,
  "corrected": "corrected version or same text if no errors",
  "translation": "Russian translation",
  "hint": "one short encouragement in Russian, max 10 words"
}
`
// Один вызов Haiku вместо трёх вызовов Sonnet
const result = await callHaiku(prompt)
```

### Обрезка истории диалога
Никогда не отправлять всю историю в API — только последние N сообщений. Старые сообщения заменять сжатым резюме.
```typescript
const MAX_HISTORY = 15

const trimHistory = (messages: Message[]): Message[] => {
  return messages.slice(-MAX_HISTORY)
}

// При вызове API всегда обрезать историю
const response = await anthropic.messages.create({
  model: 'claude-sonnet-4-20250514',
  system: SYSTEM_PROMPT,
  messages: trimHistory(conversationHistory),
  max_tokens: 400,
})
```

### Кешировать повторяющиеся переводы
Один и тот же ответ модели переводить только один раз, а результат сохранять в Supabase:
```typescript
async function getTranslation(text: string): Promise<string> {
  const hash = hashText(text)

  const { data } = await supabase
    .from('translation_cache')
    .select('translation')
    .eq('source_hash', hash)
    .single()

  if (data) return data.translation

  const translation = await callHaiku(`Переведи на русский: "${text}"`)

  await supabase.from('translation_cache').insert({
    source_hash: hash,
    source_text: text,
    translation,
  })

  return translation
}
```

### Ограничивать max_tokens по типу задачи
```typescript
const maxTokensByTask: Record<string, number> = {
  chat: 400,          // обычный диалог
  translation: 200,   // перевод
  correction: 150,    // коррекция грамматики
  hint: 80,           // подсказка пользователю
  summary: 600,       // резюме сессии
}
```

### Укорачивать системный промпт
Каждое слово в system prompt расходует токены при каждом запросе. Убирать всё, что не меняет поведение.

Плохо: «Пожалуйста, будь добрым и вежливым ассистентом, который всегда старается помочь».
Хорошо: «Тон: дружелюбный. Ответ — не длиннее 3 предложений.»

---

## Telegram-боты

### Базовая структура на grammy
```typescript
import { Bot } from 'grammy'

const bot = new Bot(process.env.TELEGRAM_BOT_TOKEN!)

bot.on('message:text', async (ctx) => {
  const response = await callClaudeAPI(ctx.message.text)
  await ctx.reply(response)
})

bot.on('message:voice', async (ctx) => {
  const file = await ctx.getFile()
  const transcript = await transcribeAudio(file.file_path!)
  const response = await callClaudeAPI(transcript)
  await ctx.reply(response)
})

bot.start()
```

### Webhook в Next.js (App Router)
```typescript
// app/api/telegram/route.ts
import { Bot } from 'grammy'

const bot = new Bot(process.env.TELEGRAM_BOT_TOKEN!)

export async function POST(req: Request) {
  const update = await req.json()
  await bot.handleUpdate(update)
  return new Response('ok')
}
```

### Регистрация webhook
```bash
curl "https://api.telegram.org/bot{TOKEN}/setWebhook?url=https://yourapp.vercel.app/api/telegram"
```

---

## Боты для ВКонтакте

### Библиотека vk-io
```bash
npm install vk-io
```

### Базовая структура VK-бота
```typescript
import { VK } from 'vk-io'

const vk = new VK({ token: process.env.VK_GROUP_TOKEN! })

vk.updates.on('message_new', async (context) => {
  if (context.isInbox) {
    const response = await callClaudeAPI(context.text ?? '')
    await context.send(response)
  }
})

vk.updates.start()
```

### Webhook для VK в Next.js
```typescript
// app/api/vk/route.ts
import { VK } from 'vk-io'

const vk = new VK({ token: process.env.VK_GROUP_TOKEN! })

export async function POST(req: Request) {
  const body = await req.json()

  // VK требует подтверждение сервера при первом подключении
  if (body.type === 'confirmation') {
    return new Response(process.env.VK_CONFIRMATION_CODE!)
  }

  await vk.updates.dispatchMiddleware(body)
  return new Response('ok')
}
```

### Особенности VK-ботов
Боты работают только через сообщества — личные аккаунты не поддерживают API ботов.
Токен создаётся в настройках сообщества → Работа с API → Создать ключ доступа.
Confirmation code — одноразовый код из настроек Callback API в сообществе.
VK проверяет secret key в каждом запросе — настраивать в разделе Callback API.
Максимальная длина сообщения во ВКонтакте — 4096 символов, контролировать через max_tokens.

---

## Боты для Max (мессенджер mail.ru)

### О платформе
Max — российский мессенджер от VK/mail.ru с аудиторией в русскоязычном сегменте.
Работает по webhook-принципу, аналогично Telegram, но API отличается.
Официальная документация: https://dev.max.ru

### Регистрация бота
Создать бота через официальный раздел для разработчиков на dev.max.ru.
Получить токен и зарегистрировать webhook-URL через API.

### Базовая структура Max-бота (Next.js)
```typescript
// app/api/max/route.ts

const MAX_API = 'https://botapi.max.ru'
const TOKEN = process.env.MAX_BOT_TOKEN!

async function sendMaxMessage(chatId: string, text: string) {
  await fetch(`${MAX_API}/messages?access_token=${TOKEN}`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
      recipient: { chat_id: chatId },
      type: 'default',
      body: { type: 'default', text },
    }),
  })
}

export async function POST(req: Request) {
  const body = await req.json()

  if (body.update_type === 'message_created') {
    const userText = body.message?.body?.text ?? ''
    const chatId = body.message?.recipient?.chat_id

    const response = await callClaudeAPI(userText)
    await sendMaxMessage(chatId, response)
  }

  return new Response('ok')
}
```

### Регистрация webhook в Max
```bash
curl -X POST "https://botapi.max.ru/subscriptions?access_token={TOKEN}" \
  -H "Content-Type: application/json" \
  -d '{"url": "https://yourapp.vercel.app/api/max"}'
```

### Особенности Max-ботов
API похоже на Telegram, но структура объектов отличается — не переносить код напрямую.
Для голосовых сообщений — использовать Whisper аналогично Telegram.
Кириллица и длинные тексты работают корректно.

---

## Хранение данных (Supabase)

```sql
-- Пользователи всех ботов в единой таблице
create table bot_users (
  id uuid primary key default gen_random_uuid(),
  platform text not null,           -- 'telegram' | 'vk' | 'max'
  platform_user_id text not null,
  username text,
  created_at timestamptz default now(),
  unique(platform, platform_user_id)
);

-- История диалогов
create table bot_sessions (
  id uuid primary key default gen_random_uuid(),
  bot_user_id uuid references bot_users(id) on delete cascade,
  messages jsonb default '[]',
  summary text,
  updated_at timestamptz default now()
);

-- Кеш переводов для экономии токенов
create table translation_cache (
  source_hash text primary key,
  source_text text not null,
  translation text not null,
  created_at timestamptz default now()
);
```

---

## Безопасность
API-ключи только в серверных переменных окружения, никогда на клиенте.
Для VK — проверять secret key в каждом входящем запросе.
Для Telegram — проверять токен через заголовок или сравнение с env.
Rate limiting: не более 20 запросов в минуту от одного пользователя.
Все вызовы к Claude и OpenAI — только через API-маршруты Next.js, не из браузера.

---

## Стиль общения
Всегда отвечать на русском языке.
Системные промпты писать на языке, на котором бот отвечает пользователям.
При проектировании новой функции сразу думать об оптимизации токенов — какую модель выбрать, можно ли объединить запросы, что кешировать.
Объяснять выбор модели и параметров — почему Haiku вместо Sonnet, почему max_tokens 400, а не 1000.
