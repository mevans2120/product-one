# Kizmet Massage & Wellness - Project Analysis

**Type:** Service Booking Website
**Client:** Massage Therapy Business (Destiny Pugh)
**Location:** Port Angeles, WA

---

## Overview

Professional booking website for a massage therapist. Features service listings, Cal.com booking integration, testimonials, and detailed policy information. Originally bootstrapped with Lovable, then migrated to Next.js.

---

## Tech Stack

| Category | Technology |
|----------|------------|
| Framework | Next.js 15.1 (App Router) |
| Language | TypeScript 5.8 |
| Styling | Tailwind CSS 3.4 + shadcn/ui |
| CMS | Sanity 4.22 |
| Forms | React Hook Form + Zod |
| State | TanStack Query 5.83 |
| Booking | Cal.com embed |
| Date Picker | react-day-picker + date-fns |
| Analytics | Vercel Analytics + GA4 |
| Deployment | Vercel |
| Package Manager | Bun |

---

## Content Architecture

**Document Types:**
- Services (duration, price, techniques, "Best For" tags)
- Testimonials (author, location, quote)

**Singleton Pages:**
- Site settings (brand, contact, business info)
- Homepage settings (hero, services preview, about preview)
- About page
- Services page settings
- Policies page
- Book page settings
- Footer settings

**Content Fetching:**
- Server-side fetch with 300s ISR
- Draft mode for preview
- GROQ queries for all content types

---

## Design System

**Brand Colors:**
- Primary: Sage green (#5a725c)
- Accent: Terracotta (#CD7F5C)
- Background: Cream (#FAF8F5)
- Text: Dark brown

**Typography:**
- Headings: Fraunces (variable font with WONK axis)
- Body: Plus Jakarta Sans

**Custom Typography Classes:**
- `.text-hero-title` - Large display text
- `.text-section-title` - Section headers
- `.text-eyebrow` - Small caps labels
- `.font-wonky` - Fraunces WONK toggle

---

## Key Features

1. **Cal.com Booking** - Embedded calendar with service selection
2. **Service Listings** - Duration, price, techniques, booking links
3. **Testimonials** - 2-up grid display
4. **Policy Pages** - Cancellation, arrival, payment info
5. **Click-to-Call** - Phone number integration
6. **Copy-to-Clipboard** - Email address
7. **Maps Integration** - Tap for directions
8. **Sanity Studio** - Embedded at /studio

---

## Project Structure Highlights

```
src/
├── app/
│   ├── page.tsx (Homepage)
│   ├── book/
│   ├── services/
│   ├── about/
│   ├── policies/
│   └── studio/
├── page-content/ (Book, Services, About, Policies)
├── components/
│   ├── ui/ (51 shadcn components)
│   ├── Navigation.tsx
│   ├── Hero.tsx
│   ├── CalEmbed.tsx
│   └── [section components]
└── sanity/
    ├── schemaTypes/
    └── lib/
```

---

## Distinguishing Characteristics

- **Booking-focused** with Cal.com integration
- **Warm, spa aesthetic** (sage/cream/terracotta palette)
- **Variable font usage** (Fraunces with WONK axis)
- **Lovable origin** - migrated from AI builder
- **Mobile-first contact** - click-to-call, copy email, tap-for-directions
- **Service-based pricing** - duration/price/techniques structure
- **Accessibility focus** - WCAG audits documented
