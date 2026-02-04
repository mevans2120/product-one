# DOA (Department of Art) - Project Analysis

**Type:** Portfolio/Marketing Website
**Client:** Set Construction Company (Film/TV/Commercial)
**Location:** Portland, OR

---

## Overview

Portfolio and marketing website for a set construction company specializing in film, TV, and commercial productions. Features project galleries, team profiles, client logos, and contact functionality. Dark, punk-rock aesthetic with aggressive design.

---

## Tech Stack

| Category | Technology |
|----------|------------|
| Framework | Next.js 15.5 (App Router, Turbopack) |
| Language | TypeScript 5.x |
| Styling | Tailwind CSS 4.x + styled-components |
| CMS | Sanity CMS |
| Forms | Custom with honeypot spam protection |
| Email | Resend + React Email |
| Testing | Jest 30 + Playwright + MSW |
| Analytics | Vercel Analytics + GA4 |
| Deployment | Vercel |

---

## Content Architecture

**Document Types:**
- Projects (portfolio pieces with credits, technical details)
- Services (offerings with icons)
- Clients (logos - standard + white versions)
- Testimonials (with ratings)
- Team members (bios, photos, IMDB links)

**Singleton Pages:**
- Site settings (title, description, social, favicon)
- Homepage settings (hero, featured items)
- About page (team, timeline)
- Contact page

**Content Fetching:**
- Server Components (SSR/SSG)
- Visual editing with draft mode
- Real-time updates via SanityLive

---

## Design System

**Brand Colors:**
- Primary: Black (#121212)
- Text: White (#FFFFFF)
- Accent: Silver (#C0C0C0)
- Light gray: (#F5F5F5)

**Typography:**
- Display: Keania One
- Heading: PT Sans
- Body: Garamond

**Animations:**
- Gradient shift (header, hero)
- Fade-in-up
- Hero pulse
- SVG path animations (curves, waves)

---

## Key Features

1. **Project Modal** - Full-screen with image gallery + swipe gestures
2. **Project Grid** - Hover effects, viewfinder corners
3. **Team Profiles** - IMDB links, credentials
4. **Client Logos** - Carousel/grid display
5. **Contact Form** - Honeypot + rate limiting + auto-reply
6. **EKG Dividers** - Animated SVG separators
7. **Draft Mode** - Sanity visual editing
8. **Comprehensive Testing** - 80% coverage minimum

---

## Project Structure Highlights

```
src/
├── app/
│   ├── page.tsx (Homepage)
│   ├── projects/
│   ├── services/
│   ├── clients/
│   ├── about/
│   ├── contact/
│   ├── studio/
│   └── api/ (contact, draft-mode, revalidate)
├── components/
│   ├── Header.tsx
│   ├── Hero.tsx
│   ├── ProjectsGrid.tsx
│   ├── ProjectModal.tsx
│   └── __tests__/
├── contexts/
├── hooks/
├── emails/ (React Email templates)
└── types/

sanity/
├── schemaTypes/
├── lib/
└── deskStructure.ts

docs/
├── TECHNICAL_ARCHITECTURE.md
├── TDD_WORKFLOW.md
└── CMS_INTEGRATION_PLAN.md
```

---

## Distinguishing Characteristics

- **Dark, aggressive aesthetic** (punk rock vibe)
- **Comprehensive testing** (Jest + Playwright + MSW)
- **TDD workflow** documented and enforced
- **Contact form security** (honeypot, rate limiting, time-based bot detection)
- **React Email templates** for auto-reply
- **Memory system** (.claude-memory + memory-bank)
- **Entertainment industry focus** (IMDB links, credits structure)
- **Swipe gestures** for mobile gallery navigation
