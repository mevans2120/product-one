# Michael Evans Portfolio - Project Analysis

**Type:** Personal Portfolio + AI Showcase
**Client:** Self (AI/ML Expert, Product Manager)
**Domain:** michael-evans-port.vercel.app

---

## Overview

Premium portfolio website showcasing professional expertise, case studies, and AI/ML projects. Features an integrated RAG chatbot, editorial case study layouts, blog system, and comprehensive design system. The most technically sophisticated of the four projects.

---

## Tech Stack

| Category | Technology |
|----------|------------|
| Framework | Next.js 15.5 (App Router, Turbopack) |
| Language | TypeScript 5.8 (strict mode) |
| Styling | Tailwind CSS 3.4 + shadcn/ui + Framer Motion |
| CMS | Sanity CMS |
| Database | Supabase (PostgreSQL + pgvector) |
| AI/LLM | Google Gemini 1.5 Pro + Claude |
| Embeddings | text-embedding-004 |
| AI SDK | Vercel AI SDK |
| Forms | React Hook Form + Zod |
| Email | Resend |
| Testing | Vitest + Playwright |
| Analytics | Vercel Analytics + Speed Insights + GA4 |
| Deployment | Vercel (Edge Runtime) |

---

## Content Architecture

**Document Types:**
- Projects/Case Studies (editorial format with metrics)
- Blog posts (Markdown + PortableText)

**Singleton Pages:**
- Profile (bio, skills, experience)
- Homepage hero (featured content)
- Services page
- Contact page
- Footer

**Chatbot Knowledge Base:**
- Interview transcripts (9 files)
- CMS content (auto-synced via webhooks)
- 170+ content chunks in vector DB

**Content Fetching:**
- Cached Sanity fetch with ISR tags
- Hybrid search (vector + BM25 keyword)
- Webhook-triggered sync (<30s latency)

---

## Design System

**Colors (HSL Variables):**
- Light: Vampire black text, off-white background
- Dark: White text, very dark background
- Accent: Purple (276° 70% 45%)
- Accent Light: Coral (25° 95% 60%)

**Typography:**
- Sans: DM Sans (headers, UI)
- Serif: Newsreader (body, editorial)
- Monospace: SFMono, Consolas

**Animation System:**
- Semantic durations (fast, normal, slow, slower)
- Smooth timing functions
- Interactive scale/opacity utilities

**Time-Based Theming:**
- Accent colors change based on Portland sunrise/sunset
- Server-side calculation to prevent FOUC

---

## Key Features

1. **RAG Chatbot** - Gemini 1.5 Pro with hybrid search
2. **Vector Search** - Supabase pgvector + BM25
3. **Editorial Case Studies** - Magazine-style layouts
4. **Horizontal Slideshow** - Scroll-snap sections
5. **Blog System** - Markdown + RSS feed
6. **Link Previews** - OG image fetching in chat
7. **Admin Dashboard** - Chat logs, sync status
8. **Dark Mode** - System preference + manual toggle
9. **Weather Integration** - Portland weather in footer
10. **Visual Regression Tests** - Playwright screenshots

---

## Project Structure Highlights

```
src/
├── app/
│   ├── (public)/ (homepage, about, blog, case-studies, services, contact)
│   ├── (admin)/
│   ├── api/
│   │   ├── chat/ (edge runtime, RAG)
│   │   ├── webhooks/sanity/
│   │   └── admin/
│   └── studio/
├── components/
│   ├── ui/ (50+ shadcn components)
│   ├── chatbot/
│   ├── navigation/
│   ├── case-studies/
│   ├── case-study-editorial/
│   └── ai-showcase-magazine/
├── lib/
│   ├── sanity/
│   └── chatbot/ (embeddings, search, sync)
└── contexts/

sanity/
├── schemas/
└── scripts/

e2e/
├── visual-regression.spec.ts
├── chatbot.e2e.spec.ts
└── snapshots/

public/chatbot-content/transcripts/ (knowledge base)
```

---

## Distinguishing Characteristics

- **AI-first architecture** (RAG chatbot, vector search)
- **Edge runtime** for sub-100ms chat latency
- **Hybrid search** combining vector + keyword
- **Auto-sync pipeline** (Sanity → webhooks → vector DB)
- **Time-based theming** (sunrise/sunset accent colors)
- **Editorial layouts** (magazine-style case studies)
- **Comprehensive testing** (Vitest + Playwright + visual regression)
- **Most documentation** (150+ docs, CLAUDE.md guide)
- **Framer Motion** for advanced animations
- **Chat analytics** logged to Supabase
