# Project Comparison: Similarities & Differences

**Date:** 2026-02-01
**Projects Analyzed:** Opal Creek, Kizmet, DOA, Michael Evans Portfolio

---

## Executive Summary

All four projects share a consistent technical foundation but diverge significantly in purpose, complexity, and specialized features. They represent a spectrum from booking-focused (Kizmet) to AI-integrated portfolio (Michael Evans).

---

## Core Similarities

### 1. Technical Foundation

| Technology | All Projects |
|------------|--------------|
| Framework | Next.js 15+ (App Router) |
| Language | TypeScript (strict mode) |
| Styling | Tailwind CSS + shadcn/ui |
| CMS | Sanity (headless) |
| Forms | React Hook Form + Zod |
| Deployment | Vercel |
| Analytics | Vercel Analytics |

**Pattern:** Every project uses the same core stack. This is your "template."

### 2. Sanity CMS Structure

All projects follow the same Sanity architecture:
- **Document types** for collections (services, posts, projects)
- **Singleton types** for page-level config (homepage, footer, settings)
- **Embedded studio** at `/studio` route
- **GROQ queries** centralized in `queries.ts`
- **Image optimization** via Sanity CDN

### 3. Component Architecture

```
components/
├── ui/           # shadcn/ui components (40-50 each)
├── Navigation    # Header with mobile menu
├── Hero          # Page hero sections
├── Footer        # Footer with links
└── [feature]/    # Feature-specific components
```

### 4. Content Fetching Pattern

```typescript
// All projects use this pattern
const data = await sanityFetch({ query: SOME_QUERY });
// With ISR revalidation (300-600 seconds)
```

### 5. File Naming Conventions

- **Pages:** `page.tsx` (App Router)
- **Components:** PascalCase (`Navigation.tsx`)
- **Utilities:** camelCase (`utils.ts`)
- **Schemas:** camelCase (`homepageSettings.ts`)

### 6. Design Token Approach

All use CSS variables (HSL format) in `globals.css`:
```css
:root {
  --color-primary: 276 70% 45%;
  --color-background: 0 0% 98%;
}
```

---

## Key Differences

### 1. Project Purpose & Complexity

| Project | Purpose | Complexity |
|---------|---------|------------|
| Kizmet | Service booking | Low-Medium |
| Opal Creek | B2B services showcase | Medium |
| DOA | Creative portfolio | Medium |
| Portfolio | AI-integrated showcase | High |

### 2. Unique Features by Project

**Kizmet (Booking-Focused):**
- Cal.com booking integration
- Click-to-call, copy-email, tap-for-directions
- Service duration/price/techniques structure
- Lovable origin (migrated from AI builder)

**Opal Creek (Service Pillars):**
- 6-color service pillar system
- Case study carousel vs singleton modes
- Extensive migration scripts (18)
- Memory bank for development tracking

**DOA (Entertainment Portfolio):**
- Dark punk-rock aesthetic
- Project modal with swipe gestures
- IMDB links for team members
- TDD workflow with 80% coverage requirement
- React Email auto-reply templates
- Honeypot + rate limiting spam protection

**Portfolio (AI-First):**
- RAG chatbot with vector search
- Supabase pgvector integration
- Edge runtime for low-latency AI
- Webhook-triggered content sync
- Time-based theming (sunrise/sunset)
- Visual regression testing

### 3. Design Aesthetics

| Project | Palette | Mood |
|---------|---------|------|
| Kizmet | Sage/Cream/Terracotta | Warm, spa-like |
| Opal Creek | Orange/Navy/Off-white | Professional, corporate |
| DOA | Black/White/Silver | Dark, aggressive |
| Portfolio | Purple/Coral/White | Modern, sophisticated |

### 4. Typography Choices

| Project | Primary Font | Style |
|---------|--------------|-------|
| Kizmet | Fraunces (variable) | Elegant, organic |
| Opal Creek | Montserrat | Clean, professional |
| DOA | Keania One + Garamond | Punk + classic |
| Portfolio | DM Sans + Newsreader | Modern + editorial |

### 5. Testing Approach

| Project | Testing Tools | Coverage |
|---------|---------------|----------|
| Kizmet | None documented | - |
| Opal Creek | None documented | - |
| DOA | Jest + Playwright + MSW | 80% minimum |
| Portfolio | Vitest + Playwright | Visual regression |

### 6. State Management

| Project | Client State | Server State |
|---------|--------------|--------------|
| Kizmet | TanStack Query | Sanity fetch |
| Opal Creek | React Query | Sanity fetch |
| DOA | Context API | Sanity fetch |
| Portfolio | TanStack Query | Sanity + Supabase |

### 7. Email/Contact Handling

| Project | Approach |
|---------|----------|
| Kizmet | Cal.com booking (external) |
| Opal Creek | Form submission API |
| DOA | Resend + React Email + spam protection |
| Portfolio | Resend |

---

## Patterns Worth Extracting

### 1. Core Template (All Projects)

```
- Next.js 15 App Router
- TypeScript strict mode
- Tailwind CSS + shadcn/ui
- Sanity CMS with embedded studio
- React Hook Form + Zod
- Vercel deployment
- ISR content fetching
```

### 2. Content Model Pattern

```
Document Types (Collections):
- Services/offerings
- Projects/case studies
- Blog posts
- Testimonials
- Team members

Singleton Types (Page Config):
- Homepage settings
- Site settings
- Footer settings
- [Page]Page settings
```

### 3. API Route Pattern

```
api/
├── contact/          # Form submission
├── draft-mode/       # Sanity preview
│   ├── enable/
│   └── disable/
└── revalidate/       # ISR invalidation
```

### 4. Component Organization

```
components/
├── ui/               # Base components (shadcn)
├── [feature]/        # Feature-specific
├── Navigation.tsx    # Header
├── Hero.tsx          # Page heroes
├── Footer.tsx        # Footer
└── [Section].tsx     # Homepage sections
```

---

## Recommendations for Platform

### What to Standardize (Template)

1. **Project scaffolding** - Next.js + TypeScript + Tailwind + shadcn
2. **Sanity setup** - Schema patterns, studio embedding, queries
3. **Component library** - shadcn/ui as base
4. **API routes** - Contact, draft-mode, revalidate
5. **Deployment** - Vercel configuration

### What to Parameterize (Per-Project)

1. **Color palette** - Brand colors as CSS variables
2. **Typography** - Font family selection
3. **Content types** - Industry-specific schemas
4. **Features** - Booking, portfolio, blog, AI chat
5. **Animations** - Project-appropriate motion

### What to Offer as Modules

1. **Booking integration** (Kizmet pattern)
2. **Case study system** (Opal Creek pattern)
3. **Portfolio gallery** (DOA pattern)
4. **AI chatbot** (Portfolio pattern)
5. **Blog system** (shared pattern)
6. **Contact forms** (shared pattern)
7. **Email templates** (DOA pattern)

---

## Complexity Tiers

| Tier | Example | Features | Build Time |
|------|---------|----------|------------|
| Basic | Landing page | Hero, about, contact, CMS | Fastest |
| Standard | Kizmet | + Booking, services, policies | Medium |
| Professional | Opal Creek/DOA | + Blog, case studies, testimonials | Longer |
| Premium | Portfolio | + AI features, advanced testing | Longest |

---

## Questions This Raises

1. **Do all clients need Sanity?** Could simpler sites use markdown or no CMS?
2. **Is shadcn/ui always right?** 50 components might be overkill for simple sites.
3. **What's the minimum viable template?** What can be removed without losing quality?
4. **Where does AI help most?** Content generation? Code? Design decisions?
5. **What slows you down?** Client feedback? Content creation? Design iteration?

---

## Next Steps

1. **Extract core template** - Common foundation across all projects
2. **Document decision points** - Where do projects diverge and why?
3. **Create feature modules** - Booking, portfolio, blog, AI as add-ons
4. **Build project scaffolder** - CLI to initialize with right features
5. **Define agent prompts** - What does each "specialist" agent need to know?
