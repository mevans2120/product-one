---
name: performance-audit
description: Systematic Lighthouse audit with fixes and verification. Eliminates the guess-and-check cycle by prioritizing fixes by impact and verifying improvements incrementally.
triggers:
  keywords:
    - "check performance"
    - "Lighthouse audit"
    - "optimize performance"
    - "site is slow"
    - "improve speed"
    - "before launch"
    - "Core Web Vitals"
    - "LCP"
    - "CLS"
    - "page speed"
    - "performance score"
  contexts:
    - "Before launch"
    - "Performance complaints from stakeholders"
    - "Low Lighthouse scores discovered"
    - "Final polish phase of project"
    - "Site feels sluggish"
  prerequisites:
    - "Site is functional and deployable"
    - "Major features are complete"
    - "Content is mostly finalized"
  anti_triggers:
    - "Still building core features (premature optimization)"
    - "No production deployment available"
    - "Performance is already ≥90 across categories"
---

# Performance Audit

This skill guides Claude through systematic Lighthouse optimization—running audits, prioritizing fixes, implementing changes, and verifying improvements. No more guess-and-check cycles.

## Why This Matters

**The Problem:**
Performance optimization often becomes:
1. Run Lighthouse → see red scores
2. Try random fixes based on intuition
3. Re-run → some things improved, some regressed
4. Repeat until frustrated or scores are "good enough"

**The Solution:**
Systematic approach: baseline → prioritize → fix one category → verify → repeat.

## Core Methodology

### Lighthouse Categories

| Category | Weight | Target |
|----------|--------|--------|
| Performance | Highest impact | ≥90 |
| Accessibility | Required for all users | ≥90 |
| Best Practices | Security + modern standards | ≥90 |
| SEO | Search visibility | ≥90 |

### The Fix-Verify Loop

```
1. Run baseline audit
   ↓
2. Identify top 5 issues by impact
   ↓
3. Fix ONE category at a time
   ↓
4. Verify fix (no regressions)
   ↓
5. Repeat until all ≥90
```

**Key rule:** Never fix multiple categories simultaneously. Verify each change.

## Process

### Phase 1: Run Baseline Audit

**Pages to audit:**
1. Homepage (most visited)
2. Main service/product page (conversion-critical)
3. Contact page (conversion endpoint)
4. Blog post (if applicable)

**Capture baseline:**
```markdown
## Baseline Audit - [Date]

### Homepage
| Category | Score | Top Issue |
|----------|-------|-----------|
| Performance | XX | [Issue] |
| Accessibility | XX | [Issue] |
| Best Practices | XX | [Issue] |
| SEO | XX | [Issue] |

### [Other pages...]
```

**Running Lighthouse:**
- Chrome DevTools → Lighthouse tab → Generate report
- Or: `npx lighthouse https://your-site.com --output html`
- Use incognito mode (no extensions interfering)
- Test mobile AND desktop (mobile usually worse)

### Phase 2: Prioritize Fixes

**Priority matrix:**

| Impact | Effort | Priority |
|--------|--------|----------|
| High | Low | 1 - Do first |
| High | High | 2 - Do second |
| Low | Low | 3 - Quick wins |
| Low | High | 4 - Skip or defer |

**Common high-impact, low-effort fixes:**

1. **Images** - next/image, proper sizing, formats
2. **Fonts** - next/font, display swap
3. **LCP element** - Preload hero image
4. **CLS** - Explicit dimensions on images/embeds
5. **Accessibility** - Color contrast, alt text, labels

### Phase 3: Fix by Category

Work through ONE category at a time:

#### Performance Fixes

**Images (usually biggest impact):**
```tsx
// BAD
<img src="/hero.jpg" alt="Hero" />

// GOOD
import Image from "next/image";

<Image
  src="/hero.jpg"
  alt="Hero"
  width={1200}
  height={600}
  priority // For above-the-fold images
/>
```

**Fonts:**
```typescript
// lib/fonts.ts - using next/font
import { Inter } from "next/font/google";

export const inter = Inter({
  subsets: ["latin"],
  display: "swap", // Prevents invisible text
  variable: "--font-inter",
});
```

**LCP optimization:**
```tsx
// Preload hero image
<Head>
  <link
    rel="preload"
    as="image"
    href="/hero.jpg"
    imageSrcSet="/hero.jpg 1x, /hero@2x.jpg 2x"
  />
</Head>
```

**JavaScript bundle:**
```tsx
// Lazy load non-critical components
import dynamic from "next/dynamic";

const HeavyComponent = dynamic(() => import("./HeavyComponent"), {
  loading: () => <Skeleton />,
  ssr: false, // If client-only
});
```

#### Accessibility Fixes

**Color contrast:**
```css
/* BAD - Light gray on white */
.text-muted {
  color: #999; /* Ratio: 2.5:1 */
}

/* GOOD - Darker gray */
.text-muted {
  color: #6b7280; /* Ratio: 4.5:1+ */
}
```

**Alt text:**
```tsx
// BAD
<Image src="/team.jpg" alt="" />
<Image src="/team.jpg" alt="image" />

// GOOD
<Image src="/team.jpg" alt="The three founders of Acme Inc standing in the office lobby" />
```

**Form labels:**
```tsx
// BAD
<input type="email" placeholder="Email" />

// GOOD
<label htmlFor="email">Email address</label>
<input id="email" type="email" aria-label="Email address" />
```

**Keyboard navigation:**
```tsx
// Ensure focusable elements
<button onClick={handleClick}>Submit</button> // Good

<div onClick={handleClick}>Submit</div> // Bad - not focusable
```

#### Best Practices Fixes

**HTTPS:**
- Ensure all resources load over HTTPS
- No mixed content warnings

**Console errors:**
- Fix all JavaScript errors
- Remove deprecated API usage

**Image aspect ratios:**
```tsx
// Set explicit dimensions
<Image
  src="/photo.jpg"
  alt="Photo"
  width={400}
  height={300}
  className="object-cover"
/>
```

#### SEO Fixes

**Meta tags:**
```tsx
// app/layout.tsx or page.tsx
export const metadata = {
  title: "Page Title | Site Name",
  description: "Compelling description under 160 characters",
  openGraph: {
    title: "Page Title",
    description: "Description for social sharing",
    images: ["/og-image.jpg"],
  },
};
```

**Semantic HTML:**
```tsx
// BAD
<div className="header">...</div>
<div className="navigation">...</div>
<div className="main-content">...</div>

// GOOD
<header>...</header>
<nav>...</nav>
<main>...</main>
```

**Heading hierarchy:**
```tsx
// BAD - Skipping levels
<h1>Title</h1>
<h3>Subtitle</h3>

// GOOD - Sequential
<h1>Title</h1>
<h2>Subtitle</h2>
```

### Phase 4: Verify Each Fix

After EACH fix:

1. **Run Lighthouse again** on affected page
2. **Check the specific metric** improved
3. **Verify no regressions** in other categories
4. **Document the change:**

```markdown
## Fix Log

### Fix 1: Hero image optimization
- **Before:** Performance 65, LCP 4.2s
- **Change:** Used next/image with priority
- **After:** Performance 78, LCP 2.1s
- **Regression:** None
```

### Phase 5: Final Verification

When all categories ≥90:

1. **Run full audit** on all key pages
2. **Test both mobile and desktop**
3. **Document final scores**
4. **Create before/after summary**

## Common Issues & Fixes

### Performance

| Issue | Fix |
|-------|-----|
| Large images | Use next/image, proper sizing |
| Render-blocking CSS | Inline critical CSS |
| Unused JavaScript | Dynamic imports, tree shaking |
| Slow LCP | Preload hero, optimize CMS images |
| High CLS | Set explicit dimensions |

### Accessibility

| Issue | Fix |
|-------|-----|
| Low contrast | Increase color contrast ratio |
| Missing alt text | Add descriptive alt attributes |
| Missing labels | Add `<label>` or aria-label |
| Non-focusable elements | Use semantic elements |
| Missing landmarks | Use `<main>`, `<nav>`, `<header>` |

### Best Practices

| Issue | Fix |
|-------|-----|
| Console errors | Fix JavaScript errors |
| Deprecated APIs | Update to modern APIs |
| Insecure requests | Use HTTPS everywhere |
| Missing ARIA | Add proper ARIA attributes |

### SEO

| Issue | Fix |
|-------|-----|
| Missing meta description | Add metadata |
| Non-descriptive links | Use descriptive text |
| Heading order | Fix hierarchy |
| Missing structured data | Add JSON-LD |

## Anti-Patterns

### Optimization

- **Don't optimize blindly** - Run audit first, prioritize by impact
- **Don't fix everything at once** - One category at a time
- **Don't skip mobile audit** - Mobile is often worse than desktop
- **Don't use unoptimized images** - Always use next/image

### Images

- **Don't lazy-load above-the-fold** - Use `priority` instead
- **Don't skip width/height** - Causes CLS
- **Don't serve oversized images** - Size appropriately

### Fonts

- **Don't use Google Fonts CDN** - Use next/font
- **Don't skip display swap** - Causes invisible text flash

### Verification

- **Don't assume fix worked** - Always re-run Lighthouse
- **Don't ignore regressions** - One fix shouldn't break another

## Checklists

### Image Optimization Checklist
See: `checklists/image-optimization.md`

### Font Optimization Checklist
See: `checklists/font-optimization.md`

### Core Web Vitals Checklist
See: `checklists/core-web-vitals.md`

### Accessibility Quick Wins
See: `checklists/accessibility-quick-wins.md`

## Quality Checklist

Before declaring performance audit complete:

- [ ] **Baseline documented** - All key pages audited
- [ ] **Performance ≥90** - On mobile and desktop
- [ ] **Accessibility ≥90** - All pages
- [ ] **Best Practices ≥90** - All pages
- [ ] **SEO ≥90** - All pages
- [ ] **No critical issues** - All red items resolved
- [ ] **Mobile tested** - Often worse than desktop
- [ ] **Fix log complete** - All changes documented

## Integration with Other Skills

**Depends on:**
- `design-system-init` - Fonts already optimized via next/font
- `sanity-setup` - CMS images need proper handling
- `cms-verify` - Content complete before optimization

**This skill runs LAST** - After site is feature-complete.

## Report Template

```markdown
# Performance Audit Report

**Project:** [Project Name]
**Date:** [Date]
**Auditor:** Claude

## Executive Summary

| Page | Before | After | Change |
|------|--------|-------|--------|
| Homepage | P:65 A:78 BP:80 S:85 | P:92 A:95 BP:95 S:100 | +27 avg |
| Services | P:60 A:75 BP:85 S:80 | P:90 A:92 BP:95 S:98 | +20 avg |

## Key Fixes Applied

1. **Image optimization** - Converted all images to next/image with proper sizing
2. **Font loading** - Migrated to next/font with display swap
3. **LCP improvement** - Added priority to hero image, preloaded critical assets
4. **Accessibility** - Fixed 12 contrast issues, added 8 missing labels

## Remaining Recommendations

1. [Any non-critical improvements]
2. [Nice-to-have optimizations]

## Verification

All scores verified on:
- [x] Production deployment
- [x] Mobile viewport
- [x] Desktop viewport
- [x] Chrome Lighthouse
```

## Success Criteria

**Performance audit is complete when:**
1. All four Lighthouse categories ≥90
2. No critical (red) issues remaining
3. Mobile scores meet threshold
4. All fixes verified with re-audit

**Measure success by:** Can you share Lighthouse scores without embarrassment?
