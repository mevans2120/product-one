# Skills Implementation Plan

**Date:** 2026-02-01
**Goal:** Create four skills to reduce iteration pain in website projects

---

## Executive Summary

Create and test four skills that address the main iteration pain points:

| Priority | Skill | Pain Point | Why This Order |
|----------|-------|------------|----------------|
| 1 | `design-system-init` | Willy-nilly design setup | Prevents downstream issues; run first on every project |
| 2 | `sanity-setup` | Presentation mode, Vercel CLI | Hit every project; well-defined steps |
| 3 | `cms-verify` | Fields not editable | Catches issues before client sees them |
| 4 | `performance-audit` | Lighthouse scores | End-of-project polish; builds on working site |

---

## Implementation Approach

### Phase 1: Design System Init

**Purpose:** Establish design tokens, colors, typography, and spacing BEFORE any component code is written.

**Triggers:**
- "new project"
- "set up design system"
- "initialize styles"
- Starting any fresh Next.js + Tailwind project

**Process the skill should enforce:**

1. **Gather brand inputs**
   - Primary/secondary/accent colors (or client brand guidelines)
   - Typography preferences (serif vs sans, mood)
   - Reference sites or aesthetic direction

2. **Generate design tokens**
   - CSS variables in `globals.css` (HSL format)
   - Tailwind config extensions
   - Typography scale (font sizes, line heights, weights)
   - Spacing scale (if non-default)

3. **Create foundational files**
   - `globals.css` with CSS variables
   - `tailwind.config.ts` with theme extensions
   - `lib/fonts.ts` with Next.js font configuration

4. **Verification checklist**
   - [ ] All colors defined as CSS variables (no hardcoded hex in components)
   - [ ] Typography scale documented
   - [ ] Fonts loaded via next/font
   - [ ] Dark mode variables defined (if applicable)
   - [ ] Sample component renders correctly with tokens

**Anti-patterns to include:**
- ❌ Don't use hardcoded colors in components
- ❌ Don't skip dark mode variables (add them even if not used initially)
- ❌ Don't use Inter (overused, "AI slop" aesthetic)
- ❌ Don't define tokens piecemeal as you go

**Files to create:**
```
~/.claude/skills/design-system-init/
├── SKILL.md
└── templates/
    ├── globals.css.template
    ├── tailwind.config.ts.template
    └── fonts.ts.template
```

**Testing approach:**
1. Start a fresh test project
2. Run skill with minimal inputs (just "spa website, warm tones")
3. Verify all files created correctly
4. Build a test component using only tokens
5. Confirm no hardcoded values needed

**Success criteria:**
- Design system complete before first component
- Zero hardcoded colors in subsequent development
- Consistent token usage across project

---

### Phase 2: Sanity Setup

**Purpose:** Configure Sanity CMS with presentation mode, embedded studio, and Vercel deployment—correctly the first time.

**Triggers:**
- "set up Sanity"
- "add CMS"
- "configure presentation mode"
- "Sanity studio"

**Process the skill should enforce:**

1. **Initialize Sanity**
   - Create sanity project (or connect existing)
   - Configure `sanity.config.ts`
   - Set up schema directory structure

2. **Embed studio in Next.js**
   - Create `/studio` route with catch-all
   - Configure CORS for localhost and production
   - Set up studio theme (optional brand colors)

3. **Configure presentation mode**
   - Install `@sanity/presentation`
   - Set up draft mode API routes (`/api/draft-mode/enable`, `/api/draft-mode/disable`)
   - Configure `draftMode()` in data fetching
   - Set up live preview with `sanityFetch` pattern

4. **Vercel integration**
   - Environment variables checklist:
     - `NEXT_PUBLIC_SANITY_PROJECT_ID`
     - `NEXT_PUBLIC_SANITY_DATASET`
     - `SANITY_API_TOKEN` (read token for preview)
     - `SANITY_REVALIDATE_SECRET` (for on-demand ISR)
   - Set up revalidation webhook
   - Configure preview deployment settings

5. **Verification checklist**
   - [ ] Studio loads at `/studio`
   - [ ] Can create/edit content in studio
   - [ ] Preview mode activates correctly
   - [ ] Changes appear in preview without deploy
   - [ ] Production fetches published content only
   - [ ] Webhook triggers revalidation

**Anti-patterns to include:**
- ❌ Don't hardcode project ID (use env vars)
- ❌ Don't skip the revalidation webhook (stale content issues)
- ❌ Don't forget CORS configuration (studio won't load)
- ❌ Don't use deprecated `preview` mode (use `draftMode`)

**Files to create:**
```
~/.claude/skills/sanity-setup/
├── SKILL.md
└── templates/
    ├── sanity.config.ts.template
    ├── studio-route.tsx.template
    ├── draft-mode-enable.ts.template
    ├── draft-mode-disable.ts.template
    ├── sanity-fetch.ts.template
    └── env-checklist.md
```

**Testing approach:**
1. Create test Next.js project
2. Run skill end-to-end
3. Verify each checkpoint works
4. Test preview mode with actual content edit
5. Deploy to Vercel preview, verify webhook

**Success criteria:**
- Zero iterations on Sanity setup
- Presentation mode works first try
- Clear environment variable checklist for deployment

---

### Phase 3: CMS Verify

**Purpose:** Verify that all CMS fields flow through from schema → query → component, catching "field not editable" issues before client review.

**Triggers:**
- "verify CMS"
- "check fields"
- "CMS complete?"
- Before client handoff
- After adding new schema fields

**Process the skill should enforce:**

1. **Inventory schemas**
   - List all document types
   - List all singleton types
   - Extract all field names per type

2. **Inventory queries**
   - Find all GROQ queries
   - Map which fields each query fetches
   - Identify missing fields (in schema but not queried)

3. **Inventory components**
   - Find components that consume CMS data
   - Check which fields are actually rendered
   - Identify hardcoded values that should be CMS-driven

4. **Generate report**
   - Fields defined but not queried
   - Fields queried but not rendered
   - Hardcoded values that could be CMS fields
   - Recommended additions

5. **Verification checklist**
   - [ ] All schema fields have corresponding query projections
   - [ ] All queried fields are used in components
   - [ ] No hardcoded text that should be editable
   - [ ] Singleton pages have all sections exposed

**Anti-patterns to include:**
- ❌ Don't assume "I'll add it later" (do it now)
- ❌ Don't hardcode section titles, CTAs, or any client-facing text
- ❌ Don't skip singleton page fields (homepage hero, footer, etc.)

**Files to create:**
```
~/.claude/skills/cms-verify/
├── SKILL.md
└── scripts/
    └── analyze-cms-flow.ts (optional automation)
```

**Testing approach:**
1. Run against existing project (Kizmet or Opal Creek)
2. Verify it catches known issues
3. Fix identified gaps
4. Re-run to confirm clean report

**Success criteria:**
- Catches missing fields before client sees them
- Clear actionable report
- Reduces CMS iteration rounds to zero

---

### Phase 4: Performance Audit

**Purpose:** Systematic Lighthouse audit with fixes and verification, eliminating the guess-and-check cycle.

**Triggers:**
- "check performance"
- "Lighthouse audit"
- "optimize for performance"
- Before launch
- "site is slow"

**Process the skill should enforce:**

1. **Run baseline audit**
   - Run Lighthouse on key pages (home, main service page, contact)
   - Capture scores: Performance, Accessibility, Best Practices, SEO
   - Identify top 5 issues by impact

2. **Prioritize fixes**
   - Categorize by effort vs impact
   - Address high-impact, low-effort first
   - Common fixes checklist:
     - [ ] Images: next/image with proper sizing
     - [ ] Fonts: next/font with display swap
     - [ ] LCP: Preload hero image
     - [ ] CLS: Set explicit dimensions on images/embeds
     - [ ] Accessibility: Color contrast, alt text, labels

3. **Apply fixes systematically**
   - One category at a time
   - Verify fix before moving to next
   - Document what was changed

4. **Re-run audit**
   - Compare before/after scores
   - Verify no regressions
   - Iterate if scores not meeting threshold

5. **Verification checklist**
   - [ ] Performance score ≥ 90
   - [ ] Accessibility score ≥ 90
   - [ ] Best Practices score ≥ 90
   - [ ] SEO score ≥ 90
   - [ ] No critical issues remaining

**Anti-patterns to include:**
- ❌ Don't optimize blindly (run audit first)
- ❌ Don't fix everything at once (one category at a time)
- ❌ Don't skip mobile audit (often worse than desktop)
- ❌ Don't use unoptimized images (always use next/image)
- ❌ Don't lazy-load above-the-fold images

**Files to create:**
```
~/.claude/skills/performance-audit/
├── SKILL.md
└── checklists/
    ├── image-optimization.md
    ├── font-optimization.md
    ├── core-web-vitals.md
    └── accessibility-quick-wins.md
```

**Testing approach:**
1. Run against existing project with known issues
2. Document baseline scores
3. Follow skill process
4. Verify score improvements
5. Confirm process catches main issues

**Success criteria:**
- All scores ≥ 90 achievable in single pass
- Clear prioritization of fixes
- No guess-and-check cycles

---

## Testing Strategy

### Per-Skill Testing

Each skill gets tested in isolation:

1. **Fresh project test** - Start from scratch, verify skill works end-to-end
2. **Existing project test** - Run against Kizmet/Opal Creek/DOA/Portfolio
3. **Edge case test** - Try with minimal inputs, unusual requirements
4. **Failure mode test** - What happens when prerequisites aren't met?

### Integration Testing

Test skills work together in sequence:

```
New Project Flow:
1. design-system-init → establishes tokens
2. [Build components]
3. sanity-setup → configure CMS
4. [Add content]
5. cms-verify → check completeness
6. [Polish]
7. performance-audit → final optimization
```

### Real Project Validation

Use next actual client project as full test:

1. Apply all four skills at appropriate phases
2. Track time spent on iteration (should be minimal)
3. Document any gaps or improvements needed
4. Iterate on skills based on real-world usage

---

## Implementation Schedule

### Week 1: Design System Init
- [ ] Write SKILL.md with full process
- [ ] Create template files
- [ ] Test on fresh project
- [ ] Test on existing project
- [ ] Refine based on testing

### Week 2: Sanity Setup
- [ ] Write SKILL.md with full process
- [ ] Create template files
- [ ] Test on fresh project
- [ ] Verify presentation mode works
- [ ] Test Vercel deployment flow

### Week 3: CMS Verify
- [ ] Write SKILL.md with full process
- [ ] Create analysis approach (manual or scripted)
- [ ] Test on Kizmet (known to have field gaps)
- [ ] Test on Opal Creek
- [ ] Refine report format

### Week 4: Performance Audit
- [ ] Write SKILL.md with full process
- [ ] Create optimization checklists
- [ ] Test on project with known Lighthouse issues
- [ ] Verify score improvements
- [ ] Document common fixes

### Week 5: Integration & Refinement
- [ ] Test full flow on test project
- [ ] Identify gaps between skills
- [ ] Refine handoffs
- [ ] Document lessons learned

---

## Success Metrics

### Quantitative
- **Iteration rounds reduced:** Target 0-1 rounds per pain point (vs 2-3 currently)
- **Lighthouse scores:** Consistently ≥ 90 across all categories
- **CMS completeness:** 100% of fields editable on first client review

### Qualitative
- Design system established before coding starts
- Sanity setup "just works" without debugging
- No "field not editable" surprises
- Performance optimization is systematic, not guesswork

---

## Open Questions

1. **Should skills include automation?** (e.g., scripts that generate files vs just instructions)
2. **How much should skills overlap?** (e.g., design-system-init might touch Tailwind config that sanity-setup also touches)
3. **Should there be a "project-init" meta-skill?** (runs design-system-init + sanity-setup in sequence)
4. **How to version skills?** (as projects evolve, skills need updates)

---

## Next Actions

1. **Start with design-system-init** (prevents most downstream issues)
2. Create skill based on design-concepts pattern
3. Test on fresh project
4. Iterate until solid
5. Move to sanity-setup
