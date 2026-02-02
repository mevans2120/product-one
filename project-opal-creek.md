# Opal Creek v2 - Project Analysis

**Type:** B2B Professional Services Website
**Client:** NetSuite ERP Consulting Firm
**Domain:** opalcreek.io

---

## Overview

Professional services website for a NetSuite ERP consulting company. Showcases six service pillars (Navigate, Stabilize, Optimize, Execute, Empower, Evaluate) with case studies, blog, testimonials, and contact functionality.

---

## Tech Stack

| Category | Technology |
|----------|------------|
| Framework | Next.js 16.1 (App Router) |
| Language | TypeScript 5.8 |
| Styling | Tailwind CSS 3.4 + shadcn/ui |
| CMS | Sanity v4.22 |
| Forms | React Hook Form + Zod |
| State | React Query 5.83 |
| Animations | Tailwind Animate |
| Icons | Lucide React |
| Analytics | Vercel Analytics + GA4/GTM |
| Deployment | Vercel |

---

## Content Architecture

**Document Types:**
- Services (6 pillars with color themes)
- Blog posts (categories, authors, rich text)
- Case studies (company info, service associations)
- Testimonials (carousel display)
- Team members (for blog authorship)

**Singleton Pages:**
- Homepage settings
- About page
- Connect page
- Footer settings
- Site settings
- Analytics settings

**Content Fetching:**
- Server Components with ISR (5-minute revalidation)
- React Query for client-side caching
- 100+ GROQ queries defined

---

## Design System

**Brand Colors:**
- Primary: Orange (#EB6F15)
- Secondary: Navy (#283043)
- Background: Off-white (#F8F8F8)

**Service Colors (6-pillar system):**
- Navigate (Purple)
- Stabilize (Orange)
- Optimize (Gray)
- Execute (Teal)
- Empower (Near-black)
- Evaluate (Green)

**Typography:**
- Font: Montserrat (400, 600, 700)

**Animations:**
- Scroll-triggered fade-in
- Parallax effects
- Staggered list animations

---

## Key Features

1. **Service Showcase** - 6 color-coded service pillars
2. **Case Studies** - Carousel or singleton display modes
3. **Blog Platform** - Categories, authors, RSS feed
4. **Testimonials** - Carousel with featured flag
5. **Contact Form** - React Hook Form + Zod validation
6. **Newsletter** - Mailchimp integration
7. **Sanity Studio** - Embedded at /studio
8. **Style Guide** - Internal design system showcase

---

## Project Structure Highlights

```
app/
├── page.tsx (Homepage)
├── about/
├── services/
├── blog/ + [slug]/
├── case-studies/ + [slug]/
├── connect/
├── studio/
└── api/ (contact, newsletter, revalidate)

components/
├── ui/ (shadcn components)
├── blog/
├── case-studies/
└── [section components]

sanity/
├── schemaTypes/ (14 document types)
├── lib/ (client, queries, live)
└── deskStructure.ts
```

---

## Distinguishing Characteristics

- **Service-centric design** with 6-color pillar system
- **Multiple case study display modes** (carousel vs singleton)
- **Comprehensive documentation** (44 doc files)
- **Memory bank system** for development tracking
- **Visual editing** with Sanity Presentation tool
- **Extensive migration scripts** (18 scripts for content migration)
