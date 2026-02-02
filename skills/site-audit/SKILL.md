---
name: site-audit
description: Comprehensive audit of an existing website before redesign. Extracts brand assets, inventories content, captures technical baseline, and identifies what works vs. what needs to change. Creates a structured foundation for redesign projects.
triggers:
  keywords:
    - "audit existing site"
    - "analyze current website"
    - "site redesign"
    - "what do we have"
    - "review current site"
    - "website audit"
    - "before we redesign"
    - "existing site analysis"
    - "content inventory"
    - "brand extraction"
  contexts:
    - "Starting a redesign project"
    - "Client has existing website to replace"
    - "Need to understand what to migrate"
    - "Evaluating current site performance"
    - "Extracting brand assets for new build"
  prerequisites:
    - "Existing website URL available"
    - "Access to view the live site"
    - "Ideally: access to backend/CMS for content export"
  anti_triggers:
    - "Building from scratch with no existing site"
    - "Site already audited, ready to build"
    - "Just need performance check (use performance-audit)"
---

# Site Audit

This skill guides Claude through a comprehensive audit of an existing website before a redesign. It extracts brand assets, inventories content, captures technical baselines, and identifies what's working vs. what needs to change.

## Why This Matters

**The Problem:**
Redesign projects often fail because:
- Existing brand assets weren't extracted (recreating logos, finding colors)
- Content was forgotten during migration ("where's our privacy policy?")
- Technical problems from the old site get repeated
- What was working well gets thrown out with what wasn't

**The Solution:**
Systematic audit that captures everything before building anything new.

## Core Methodology

### The Four Pillars of Site Audit

```
┌─────────────────────────────────────────────────────────────┐
│  1. BRAND EXTRACTION                                        │
│     Colors, typography, logos, visual patterns              │
├─────────────────────────────────────────────────────────────┤
│  2. CONTENT INVENTORY                                       │
│     Pages, sections, text, images, documents                │
├─────────────────────────────────────────────────────────────┤
│  3. TECHNICAL BASELINE                                      │
│     Performance, accessibility, SEO, mobile experience      │
├─────────────────────────────────────────────────────────────┤
│  4. CONVERSION & UX AUDIT                                   │
│     CTAs, forms, user flows, what's working/broken          │
└─────────────────────────────────────────────────────────────┘
```

## Process

### Phase 1: Brand Extraction

**Goal:** Capture all visual brand elements for reference or evolution.

#### Colors

1. **Extract primary colors** from:
   - Logo
   - Navigation/header
   - Buttons/CTAs
   - Links
   - Background colors

2. **Document in hex and HSL:**
```markdown
| Role | Hex | HSL | Usage |
|------|-----|-----|-------|
| Primary | #1a365d | 216 57% 23% | Navigation, headings |
| Accent | #d69e2e | 43 70% 51% | CTAs, highlights |
| Background | #f7fafc | 204 45% 98% | Page background |
| Text | #2d3748 | 220 26% 24% | Body text |
```

**Tools:**
- Browser DevTools → Computed styles
- ColorZilla extension
- Site palette extractors

#### Typography

1. **Identify fonts in use:**
   - Headings (h1-h6)
   - Body text
   - Navigation
   - Special elements (quotes, captions)

2. **Document font stack:**
```markdown
| Element | Font Family | Weight | Size | Source |
|---------|-------------|--------|------|--------|
| H1 | Playfair Display | 700 | 48px | Google Fonts |
| Body | Open Sans | 400 | 16px | Google Fonts |
| Nav | Open Sans | 600 | 14px | Google Fonts |
```

**How to find:**
- DevTools → Computed → font-family
- WhatFont extension
- Check `<link>` tags for Google Fonts

#### Logo & Assets

1. **Download/screenshot:**
   - Primary logo (full color)
   - Logo variations (white, dark, icon-only)
   - Favicon
   - Social media images

2. **Note file formats available:**
   - SVG (preferred)
   - PNG (with transparency?)
   - Original source files (if accessible)

3. **Document usage:**
```markdown
| Asset | Location | Format | Notes |
|-------|----------|--------|-------|
| Primary logo | Header | PNG | No SVG available |
| White logo | Footer | PNG | On dark background |
| Favicon | Browser tab | ICO | Low quality |
```

#### Visual Patterns

Document recurring visual elements:
- Button styles (rounded? shadows? hover states?)
- Card patterns
- Section dividers
- Background treatments (gradients, patterns, images)
- Icon style (outline? filled? custom illustrations?)

### Phase 2: Content Inventory

**Goal:** Know exactly what content exists and what needs to migrate.

#### Page Inventory

Create a complete sitemap:

```markdown
## Site Structure

### Main Navigation
- Home
- About
  - Our Story
  - Team
- Services
  - Service A
  - Service B
  - Service C
- Blog (47 posts)
- Contact

### Footer Links
- Privacy Policy
- Terms of Service
- FAQ

### Hidden/Orphan Pages
- /old-landing-page
- /webinar-2023
```

#### Content Per Page

For each key page, document:

```markdown
## Homepage

### Sections (top to bottom)
1. **Hero**
   - Headline: "Transform Your Career"
   - Subhead: "Expert coaching for professionals..."
   - CTA: "Book a Call" → /contact
   - Background: Video (career-montage.mp4)

2. **Social Proof Bar**
   - Logos: Forbes, Today Show, WSJ, SiriusXM
   - No links, just images

3. **Services Overview**
   - 3 cards with icons
   - Links to individual service pages

4. **Testimonials**
   - Carousel, 5 testimonials
   - Photo + name + title + quote

5. **About Preview**
   - Photo of founder
   - Brief bio (127 words)
   - CTA: "Learn More" → /about

6. **Newsletter Signup**
   - Headline + email input
   - Mailchimp integration

7. **Footer**
   - Logo, nav links, social icons, copyright
```

#### Content Migration Checklist

```markdown
## Migration Requirements

### Must Migrate
- [ ] All service page content
- [ ] Team bios and photos
- [ ] Blog posts (47) with images
- [ ] Testimonials (12)
- [ ] Privacy policy
- [ ] Terms of service

### Consider Migrating
- [ ] Old landing pages (check analytics for traffic)
- [ ] FAQ content (merge into relevant pages?)

### Do Not Migrate
- [ ] Outdated event pages
- [ ] Broken resource links
- [ ] Test pages
```

#### Media Assets

```markdown
## Media Inventory

### Images
- Hero images: 5
- Team photos: 4
- Blog post images: ~120
- Testimonial photos: 12
- Service icons: 8

### Documents
- Workbook PDF (for sale)
- Free guide PDF (lead magnet)
- Media kit PDF

### Videos
- Hero background video
- YouTube embeds in blog (15)

### Downloads Needed
- [ ] All team photos (high res if possible)
- [ ] Product images
- [ ] Downloadable PDFs
```

### Phase 3: Technical Baseline

**Goal:** Understand current performance to ensure the rebuild is better.

#### Lighthouse Audit

Run Lighthouse on key pages:

```markdown
## Technical Baseline - [Date]

### Homepage
| Category | Score | Top Issue |
|----------|-------|-----------|
| Performance | 45 | Unoptimized images (2.4MB hero) |
| Accessibility | 62 | Low contrast text, missing labels |
| Best Practices | 78 | Mixed content warnings |
| SEO | 85 | Missing meta descriptions |

### Service Page
| Category | Score | Top Issue |
|----------|-------|-----------|
| Performance | 52 | Render-blocking scripts |
| Accessibility | 58 | No alt text on images |
| Best Practices | 85 | OK |
| SEO | 70 | Thin content |
```

#### Mobile Experience

- [ ] Is there a mobile menu?
- [ ] Do forms work on mobile?
- [ ] Are tap targets large enough?
- [ ] Any horizontal scroll issues?
- [ ] Is text readable without zooming?

#### Current Tech Stack

```markdown
## Current Technology

| Component | Technology | Notes |
|-----------|------------|-------|
| Platform | WordPress 6.2 | Outdated |
| Theme | Divi | Page builder |
| Hosting | GoDaddy | Slow response times |
| Forms | Contact Form 7 | Works but basic |
| Analytics | Google Analytics | UA (needs GA4 migration) |
| Email | Mailchimp | Keep integration |
| eCommerce | WooCommerce | 3 products |
| Booking | Calendly embed | Keep |
```

#### SEO Baseline

```markdown
## SEO Status

### Domain Authority
- DA: 35 (via Moz/Ahrefs)
- Backlinks: 127

### Rankings (check Search Console)
- "career coach NYC" - Position 8
- "executive coaching" - Position 23
- Brand name - Position 1

### Technical SEO
- [ ] XML sitemap exists?
- [ ] Robots.txt configured?
- [ ] SSL certificate valid?
- [ ] 301 redirects needed for URL changes?

### Indexed Pages
- Google: 67 pages indexed
- Check for: outdated pages, duplicate content
```

### Phase 4: Conversion & UX Audit

**Goal:** Understand what's working to keep, what's broken to fix.

#### Current Conversion Points

```markdown
## Conversion Audit

### Primary CTAs
| CTA | Location | Destination | Friction |
|-----|----------|-------------|----------|
| "Book a Call" | Hero, nav | Calendly popup | Low - good |
| "Buy Workbook" | Services page | WooCommerce cart | High - 5 steps |
| "Subscribe" | Footer | Mailchimp form | Medium - no incentive |

### Forms
| Form | Location | Fields | Issues |
|------|----------|--------|--------|
| Contact | /contact | 6 fields | Too many fields, no validation |
| Newsletter | Footer | 1 field | No confirmation message |
| Booking | Calendly | External | Works well |

### Conversion Blockers Observed
1. No clear value proposition on homepage
2. Testimonials buried below fold
3. Services page has no pricing
4. Mobile menu hard to tap
5. Contact form too long
```

#### User Flow Analysis

```markdown
## Key User Flows

### Flow 1: Book a Consultation
Home → Services → [reads] → Contact → [form] → ???
- **Issue:** No confirmation, no next steps
- **Improvement:** Thank you page with expectations

### Flow 2: Buy Workbook
Home → Services → Workbook page → Add to cart → Cart → Checkout
- **Issue:** 5 steps, cart abandonment likely
- **Improvement:** Direct checkout button

### Flow 3: Learn About Services
Home → Services → [specific service]
- **Issue:** Service pages are thin, no differentiation
- **Improvement:** More detailed service pages with outcomes
```

#### What's Working

Document things to preserve:
```markdown
## What's Working Well

1. **Calendly integration** - Easy booking, keep this
2. **Testimonial content** - Strong quotes, real photos
3. **Blog content** - Good SEO traffic, quality posts
4. **Media logos** - Strong credibility signals
5. **Mobile menu** - Actually works (rare for Divi)
```

#### What's Broken

```markdown
## Issues to Fix in Redesign

### Critical
1. Homepage has no clear value proposition
2. 45 performance score killing conversions
3. No mobile-optimized forms
4. Checkout flow has 60% abandonment

### Important
1. Inconsistent button styles
2. Blog has no categories/filtering
3. Team page photos are different sizes
4. Footer links are broken

### Nice to Fix
1. Favicon is blurry
2. Social links open in same tab
3. Copyright year is 2022
```

## Output: Audit Report

Generate a comprehensive report using the template.

See: `templates/audit-report.md`

## Anti-Patterns

### Audit Process

- **Don't skip the screenshot step** - Sites change, capture what exists now
- **Don't assume you can access everything later** - Download assets immediately
- **Don't audit only the homepage** - Key pages differ significantly
- **Don't forget mobile** - Often worse than desktop

### Content

- **Don't assume all content migrates** - Some should be retired
- **Don't miss legal pages** - Privacy policy, terms are required
- **Don't forget downloadable assets** - PDFs, documents, media

### Technical

- **Don't skip the baseline** - You need numbers to prove the rebuild is better
- **Don't ignore SEO rankings** - URL changes can tank rankings without redirects
- **Don't miss integrations** - Email, booking, analytics need to reconnect

## Quality Checklist

Before declaring audit complete:

- [ ] **Colors extracted** - All brand colors documented with hex/HSL
- [ ] **Fonts identified** - All typefaces documented with sources
- [ ] **Logo assets downloaded** - All variations saved locally
- [ ] **Page inventory complete** - Every page cataloged
- [ ] **Content sections documented** - Key pages broken down
- [ ] **Migration list created** - What moves, what doesn't
- [ ] **Lighthouse baseline captured** - Scores for key pages
- [ ] **Mobile tested** - Issues documented
- [ ] **Tech stack documented** - Current platform and integrations
- [ ] **Conversion points mapped** - CTAs, forms, flows
- [ ] **What's working identified** - Don't throw out the good
- [ ] **Issues prioritized** - Critical, important, nice-to-have

## Integration with Other Skills

**This skill comes FIRST** for redesign projects.

**Outputs feed into:**
- `design-system-init` - Extracted colors/fonts inform token decisions
- `sanity-setup` - Content inventory informs schema design
- `cms-verify` - Migration list becomes verification checklist
- `performance-audit` - Baseline scores are the "before" to beat

## Example Workflow

```
1. User: "We're redesigning maggiemistal.com"

2. Claude runs site-audit:
   - Extracts brand colors (turquoise, maroon)
   - Documents fonts (current typography)
   - Inventories 15 pages + 47 blog posts
   - Captures Lighthouse baseline (Performance: 45)
   - Maps conversion flows (Calendly works, WooCommerce doesn't)
   - Identifies wins (testimonials, media logos) and issues (no value prop)

3. Claude generates audit report:
   - Brand extraction summary
   - Content migration checklist
   - Technical baseline
   - Recommendations

4. Audit informs next steps:
   - design-system-init: Evolve turquoise/maroon or new direction?
   - Schema design: 3 products, 47 posts, 12 testimonials
   - Performance target: Beat current 45 score
```

## Success Criteria

**Site audit is complete when:**
1. Someone could rebuild the brand visuals from your documentation
2. No content is "discovered" later that should have migrated
3. Technical baseline exists to prove improvement
4. Clear list of what to keep vs. change

**Measure success by:** Does the client say "you captured everything" or "wait, what about X"?
