---
name: pdf-creator
description: Create, generate or export PDF files in code. Use when user needs to produce a PDF programmatically — reports, invoices, certificates, documentation, exports from web apps. Trigger when user says "сделай PDF", "сгенерируй отчёт в PDF", "экспорт в PDF", "создай PDF из данных", or wants to convert HTML/Markdown to a downloadable PDF file in a Next.js or Python project.
---

# PDF Creator

## Назначение
Создавать PDF-файлы программно — в Next.js-проектах, Python-скриптах и SaaS-приложениях.
Применять, когда нужно сгенерировать отчёт, сертификат, инвойс, экспорт данных или любой другой документ в формате PDF.

## Выбор инструмента

### В Next.js / TypeScript-проектах
Предпочтительные библиотеки в порядке приоритета:

**@react-pdf/renderer** — лучший выбор, когда нужна гибкая вёрстка с компонентами React.
```bash
npm install @react-pdf/renderer
```
Использовать для: сертификатов, отчётов, структурированных документов с брендингом.

**Puppeteer / headless Chrome** — когда нужно распечатать существующую HTML-страницу точно как она выглядит в браузере.
```bash
npm install puppeteer
```
Использовать для: экспорта дашбордов, скриншотов интерфейса, сложных CSS-макетов.

**jsPDF** — лёгкий вариант для простых текстовых документов без сложной вёрстки.
```bash
npm install jspdf
```

### В Python-проектах (FastAPI / скрипты)
**WeasyPrint** — конвертирует HTML+CSS в PDF, удобен когда шаблон уже есть в HTML.
```bash
pip install weasyprint
```

**ReportLab** — мощный инструмент для сложных документов с таблицами, графиками, кастомными шрифтами.
```bash
pip install reportlab
```

**pdfkit** — обёртка над wkhtmltopdf, хорошо работает с готовыми HTML-шаблонами.

## Паттерны реализации

### Серверный рендеринг PDF в Next.js (App Router)
```typescript
// app/api/generate-pdf/route.ts
import { NextResponse } from 'next/server'

export async function POST(req: Request) {
  const data = await req.json()
  // генерация PDF через @react-pdf/renderer или puppeteer
  const pdfBuffer = await generatePdf(data)
  
  return new NextResponse(pdfBuffer, {
    headers: {
      'Content-Type': 'application/pdf',
      'Content-Disposition': 'attachment; filename="report.pdf"',
    },
  })
}
```

### Клиентский рендеринг (только для лёгких документов)
Использовать только когда данные доступны на клиенте и документ простой.
Тяжёлые документы всегда рендерить на сервере.

## Типичные сценарии у Natali

**VoiceReels** — сертификат об озвучке или отчёт о сессии.
**Blog Monetization Navigator** — PDF-отчёт с результатами диагностики, готовый для скачивания.
**borisova.one** — экспорт кейса или CV в PDF.
**Клиентские проекты** — инвойсы, брифы, презентационные PDF.

## Важные нюансы
- Кириллические шрифты требуют явного подключения — не полагаться на системные.
- В Vercel окружении Puppeteer требует `puppeteer-core` + `@sparticuz/chromium`.
- Размер PDF влияет на скорость загрузки — оптимизировать изображения перед вставкой.
- Для продакшена всегда отдавать PDF через API-маршрут, а не генерировать на клиенте.

## Стиль общения
Всегда отвечать на русском языке.
Давать готовый код, объяснять выбор библиотеки и показывать конкретный паттерн под стек пользователя.
