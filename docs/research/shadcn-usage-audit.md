# shadcn/ui Usage Audit

**Date:** 2026-02-01
**Projects Analyzed:** Opal Creek, Kizmet, DOA, Michael Evans Portfolio

---

## Executive Summary

| Project | Total Components | Used | Unused | Usage Rate |
|---------|------------------|------|--------|------------|
| Opal Creek | 16 | 13 | 2 | 81% |
| Kizmet | 48 | 6 | 33 | 12.5% |
| DOA | 0 | — | — | N/A (custom) |
| Portfolio | 45 | 15 | 30 | 33% |

**Key Finding:** DOA doesn't use shadcn/ui at all—it has a custom component library built with Tailwind + styled-components. This proves the "build your own" approach is viable.

---

## Project-by-Project Breakdown

### Opal Creek (Most Efficient)

**16 components, 13 used (81%)**

**Used:**
| Component | Import Locations |
|-----------|------------------|
| button | ContactSection, ContactForm, CaseStudyCTA, NewsletterSignupDialog, HeroClient |
| card | ServicesPage, ContactPage, AboutPage, ContactForm, ServicesSection, Testimonials |
| dialog | NewsletterSignupDialog |
| enhanced-input | ContactForm, NewsletterSignupDialog |
| floating-input | ContactPage |
| form | ContactPage |
| select | ContactForm |
| sonner | ClientProviders |
| textarea | ContactForm, ContactPage |
| toast | ClientProviders, toaster, use-toast |
| toaster | ClientProviders |
| tooltip | ClientProviders |
| use-toast | hook wrapper |

**Unused:**
- input.tsx
- skeleton.tsx

---

### Kizmet (Most Bloated)

**48 components, only 6 actively used (12.5%)**

**Used:**
| Component | Import Locations |
|-----------|------------------|
| button | About, Book, CTASection, Hero, Navigation, not-found, Services, ServicesPreview |
| card | Services, ServicesPreview |
| sonner | providers |
| toast | providers, use-toast |
| toaster | providers |
| tooltip | providers |

**Internal Dependencies Only (7):**
dialog, input, label, separator, sheet, skeleton, toggle

**Completely Unused (33):**
accordion, alert, alert-dialog, aspect-ratio, avatar, badge, breadcrumb, calendar, carousel, chart, checkbox, collapsible, command, context-menu, drawer, dropdown-menu, form, hover-card, input-otp, menubar, navigation-menu, pagination, popover, progress, radio-group, resizable, scroll-area, select, sidebar, slider, switch, table, tabs, textarea, toggle-group

---

### DOA (No shadcn/ui)

**Custom component library—no shadcn/ui installed**

Uses:
- Tailwind CSS 4
- styled-components
- clsx + tailwind-merge

Custom components built:
- AboutCTA, ClientLogos, ContactClient, ContactCTA
- EKGDivider, HeartbeatDivider (brand-specific)
- Footer, Header, Hero
- ProjectModal, Projects, ProjectsClient, ProjectsGrid, ProjectSlideshow
- RichText, ServiceIcons, Services, Testimonials
- ViewfinderCorners (unique visual treatment)

**Insight:** This project proves you can build differentiated, custom components without shadcn/ui dependency.

---

### Portfolio (Moderate Usage)

**45 components, 15 used (33%)**

**Used:**
| Component | Import Locations |
|-----------|------------------|
| button | ChatLogsClient, BentoImageBehind, ContactForm, ChatbotContentClient |
| card | ChatLogsClient, ChatbotContentClient |
| scroll-area | ChatLogsClient |
| badge | ChatLogsClient |
| carousel | ImageCarouselLightbox |
| dialog | ImageCarouselLightbox |
| floating-label-input | ContactForm, layout |
| input | layout |
| separator | page (homepage) |
| OrnamentalCTA | page, CaseStudiesHero, RelatedPostsClient, ViewMoreProjectsClient |
| skeleton | LinkPreview |
| table | ContentTable |
| theme-toggle | Footer |
| toaster | layout |
| sonner | layout |

**Unused (30):**
accordion, alert-dialog, alert, aspect-ratio, avatar, chart, checkbox, collapsible, context-menu, dropdown-menu, form, hover-card, input-otp, label, menubar, navigation-menu, popover, progress, radio-group, resizable, select, sheet, slider, switch, tabs, textarea, time-theme-toggle, toast, toggle-group, toggle, tooltip

---

## Cross-Project Analysis

### Universal Components (Used in All shadcn Projects)

| Component | Opal Creek | Kizmet | Portfolio |
|-----------|------------|--------|-----------|
| button | ✓ | ✓ | ✓ |
| card | ✓ | ✓ | ✓ |
| sonner | ✓ | ✓ | ✓ |
| toaster | ✓ | ✓ | ✓ |

### Commonly Used (2+ Projects)

| Component | Opal Creek | Kizmet | Portfolio |
|-----------|------------|--------|-----------|
| tooltip | ✓ | ✓ | — |
| toast | ✓ | ✓ | — |
| dialog | ✓ | — | ✓ |
| input | — | internal | ✓ |
| skeleton | — | internal | ✓ |

### Project-Specific Components

**Opal Creek only:**
- enhanced-input, floating-input, form, select, textarea

**Portfolio only:**
- badge, carousel, scroll-area, table, separator, OrnamentalCTA (custom), theme-toggle

---

## Recommendations

### 1. Minimal Starter Set (6 Components)

Based on universal usage, a new project template should include only:

```
components/ui/
├── button.tsx      # CTAs, form submits, navigation
├── card.tsx        # Content containers, service cards
├── dialog.tsx      # Modals, confirmations
├── sonner.tsx      # Toast notifications (modern)
├── toaster.tsx     # Toast container
└── tooltip.tsx     # Hover hints
```

**Everything else should be added on-demand.**

### 2. Consider the DOA Approach

DOA proves you don't need shadcn/ui. Benefits:
- Fully custom, brand-specific components
- No bloat from unused imports
- Easier for agents to reason about (fewer files)
- Differentiator vs other Next.js sites

Trade-off: More upfront work per component.

### 3. Cleanup Opportunities

| Project | Components to Remove | Bundle Size Savings |
|---------|----------------------|---------------------|
| Kizmet | 33+ unused | Significant |
| Portfolio | 30 unused | Moderate |
| Opal Creek | 2 unused | Minimal |

### 4. Hybrid Approach

Use shadcn/ui for:
- button (well-tested accessibility)
- dialog (complex a11y requirements)
- toast system (animation handling)

Build custom for:
- Cards (simple, easy to customize)
- Navigation (always brand-specific)
- Section components (unique per project)

---

## Next Steps

1. **Kizmet cleanup** - Remove 33 unused components
2. **Portfolio cleanup** - Remove 30 unused components
3. **Template decision** - Choose between:
   - Minimal shadcn starter (6 components)
   - DOA-style custom library
   - Hybrid approach
4. **Document component patterns** - If going custom, document the patterns from DOA

---

## Questions This Raises

1. **Why did Kizmet end up with 48 components?** Was this from a scaffold command that added everything?
2. **Could DOA's custom components become the basis for a shared library?** They're already tested in production.
3. **What's the real bundle size impact?** Tree-shaking should handle unused imports, but the cognitive load on agents remains.
