# 🧠 Cursor Product Kit

> Cursor rules, skills and global context for product managers, marketers and vibe-coders who build digital products with AI.

A battle-tested setup for working with the Cursor AI agent — built by a product strategist who ships websites, landing pages, Telegram bots, AI assistants and SaaS products using Cursor every day.

---

## ✨ What's inside

### 📋 Rules — always-on agent behavior
Rules guide how the Cursor agent thinks and acts across all your projects.

| File | What it does |
|------|-------------|
| `GLOBAL_CONTEXT_7.md` | Who you are, your stack, communication style, MCP tools |
| `global_deploy_safety.md` | Prevents accidental deploys to wrong projects |
| `frontend_architecture.md` | Next.js, TypeScript, Tailwind — how to build UI |
| `backend_architecture.md` | API routes, Telegram bots, AI integrations, n8n |
| `safe_editing.md` | Never break existing code, minimal precise edits |
| `debugging_workflow.md` | Analyze before fixing, root cause first |
| `typescript_engineering.md` | Type safety, no `any`, readable types |
| `ux_product_thinking.md` | Think like a PM before writing a single line |

### 🛠 Skills — specialized agent capabilities
Skills are triggered automatically when relevant, or manually with `/` in Cursor chat.

| Skill | Triggers when... |
|-------|-----------------|
| `copywriter` | Writing or improving Russian text |
| `product-analyst` | Analyzing UX flows, product logic, SaaS |
| `digital-trends-analyst` | Evaluating if design feels modern |
| `smm-analyst` | Reviewing social media profiles or posts |
| `ai-bot-builder` | Building AI assistants or Telegram bots |
| `landing-page-builder` | Creating or improving landing pages |
| `presentation-designer` | Structuring slide decks and pitches |
| `n8n-automation` | Designing automation workflows |
| `marketolog` | Marketing strategy, launches, funnels |
| `saas-architect` | Database schema, auth, API architecture |
| `cursor-task-writer` | Writing structured `.md` tasks for Cursor agent |
| `pdf-creator` | Generating PDFs programmatically |

---

## 🚀 How to install

### Option 1 — Global (works in all projects)
Copy files into your global Cursor config:

```bash
# Rules
cp rules/*.md ~/.cursor/rules/

# Skills
cp skills/*.md ~/.cursor/skills/
```

### Option 2 — Per project
Copy into your project folder:

```bash
cp rules/*.md .cursor/rules/
cp skills/*.md .cursor/skills/
```

Then open Cursor → Settings → Rules, Skills, Subagents to verify everything loaded.

---

## ⚙️ MCP servers (recommended)

This kit works best with these MCP servers connected in Cursor:

| Server | Install |
|--------|---------|
| context7 | `npx -y @upstash/context7-mcp@latest` |
| Figma | `npx -y figma-developer-mcp --stdio` |
| Playwright | `npx -y @playwright/mcp@latest` |
| Miro | `url: https://mcp.miro.com/` |

Add to `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "context7": {
      "command": "npx",
      "args": ["-y", "@upstash/context7-mcp@latest"]
    },
    "figma": {
      "command": "npx",
      "args": ["-y", "figma-developer-mcp", "--stdio"],
      "env": {
        "FIGMA_API_KEY": "your_figma_api_key"
      }
    },
    "playwright": {
      "command": "npx",
      "args": ["-y", "@playwright/mcp@latest"]
    },
    "miro": {
      "url": "https://mcp.miro.com/",
      "disabled": false
    }
  }
}
```

---

## 🔧 Personalization

Several files contain personal context about the author (Natali Borisova) — her projects, stack and tone of voice. **Replace with your own data** before using:

**In `GLOBAL_CONTEXT_7.md`:**
- Your name and role
- Your tools and OS
- Your active projects and stack

**In skills files** — search for these and replace:
- `Natali Borisova` → your name
- `VoiceReels`, `Blog Monetization Navigator`, `borisova.one` → your projects
- Adjust tone of voice descriptions to match your style

Everything else (rules, architecture principles, skill logic) is universal and works as-is.

---

## 📁 Repository structure

```
cursor-product-kit/
├── rules/
│   ├── GLOBAL_CONTEXT_7.md
│   ├── global_deploy_safety.md
│   ├── frontend_architecture.md
│   ├── backend_architecture.md
│   ├── safe_editing.md
│   ├── debugging_workflow.md
│   ├── typescript_engineering.md
│   └── ux_product_thinking.md
├── skills/
│   ├── copywriter.md
│   ├── product-analyst.md
│   ├── digital-trends-analyst.md
│   ├── smm-analyst.md
│   ├── ai-bot-builder.md
│   ├── landing-page-builder.md
│   ├── presentation-designer.md
│   ├── n8n-automation.md
│   ├── marketolog.md
│   ├── saas-architect.md
│   ├── cursor-task-writer.md
│   └── pdf-creator.md
├── mcp.json
└── README.md
```

---

## 🙋 Who is this for

- Product managers who vibe-code with Cursor
- Marketers building their own digital tools
- Founders shipping SaaS, landing pages and bots
- Anyone who wants Cursor to behave like a senior PM + engineer combo

---

## ⭐ If this helped you

Give it a star — it helps others find this kit.
Feel free to fork, adapt and share with your team.

---

Built by [Natali Borisova](https://borisova.one) — product strategist, vibe-coder, AI builder.
