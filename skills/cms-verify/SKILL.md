---
name: cms-verify
description: Verify that all CMS fields flow through from schema to query to component. Catches "field not editable" issues before client review by systematically checking schema → query → component completeness.
triggers:
  keywords:
    - "verify CMS"
    - "check fields"
    - "CMS complete"
    - "field check"
    - "schema audit"
    - "missing fields"
    - "not editable"
    - "hardcoded text"
    - "before handoff"
    - "client review prep"
  contexts:
    - "Before client handoff"
    - "After adding new schema fields"
    - "Debugging 'why can't I edit this?'"
    - "CMS integration complete check"
    - "Quality assurance before launch"
  prerequisites:
    - "Sanity schemas defined"
    - "GROQ queries written"
    - "Components consuming CMS data exist"
  anti_triggers:
    - "Just starting CMS setup (use sanity-setup)"
    - "No CMS in project"
    - "Only checking one specific field"
---

# CMS Verify

This skill guides Claude through verifying that all CMS fields flow correctly from Sanity schema → GROQ query → React component. It catches "field not editable" issues before client review.

## Why This Matters

**The Problem:**
The most frustrating client feedback: "I can't edit this text." This happens when:
- Field exists in schema but not queried
- Field is queried but not rendered in component
- Text is hardcoded instead of using CMS data
- Singleton page fields are missing

**The Solution:**
Systematic verification of the complete data flow before any client sees the site.

## Core Methodology

### The Three-Layer Check

Every piece of editable content must exist in ALL three places:

```
┌─────────────────────────────────────────────────────────────┐
│  1. SCHEMA (sanity/schemas/*.ts)                            │
│     Field is DEFINED with name, type, title                 │
├─────────────────────────────────────────────────────────────┤
│  2. QUERY (sanity/lib/queries.ts or inline)                 │
│     Field is SELECTED in GROQ projection                    │
├─────────────────────────────────────────────────────────────┤
│  3. COMPONENT (components/*.tsx or app/**/page.tsx)         │
│     Field is RENDERED or used in the UI                     │
└─────────────────────────────────────────────────────────────┘
```

A break at ANY layer = content not editable.

## Process

### Phase 1: Inventory Schemas

**Step 1: List all document types**

Find all schema files and catalog:
- Document types (collections: posts, services, projects)
- Singleton types (page settings: homepage, footer, about)
- Object types (reusable: portableText, link, seo)

**Step 2: Extract fields per type**

For each document/singleton type, list:
- Field name
- Field type
- Whether it's user-facing content (text, image, link)

**Example inventory:**
```markdown
## homepage (singleton)

| Field | Type | User-Facing? |
|-------|------|--------------|
| hero.headline | string | Yes |
| hero.subheadline | text | Yes |
| hero.ctaText | string | Yes |
| hero.ctaLink | url | Yes |
| hero.backgroundImage | image | Yes |
| services | array[reference] | Yes |
| testimonials | array[reference] | Yes |
```

### Phase 2: Inventory Queries

**Step 1: Find all GROQ queries**

Look in:
- `sanity/lib/queries.ts` (centralized)
- Page components with inline queries
- API routes with data fetching

**Step 2: Map field coverage**

For each query, list which fields are projected:

```groq
// Example: Homepage query
*[_type == "homepage"][0] {
  "hero": {
    headline,
    subheadline,
    ctaText,
    ctaLink,
    "backgroundImage": backgroundImage.asset->url
  },
  "services": services[]->{
    _id,
    title,
    slug
  }
}
```

**Missing from query:** testimonials (defined in schema but not selected)

### Phase 3: Inventory Components

**Step 1: Find components that render CMS data**

Trace from page → components:
- Which component renders each field?
- Are there any hardcoded values?

**Step 2: Check for hardcoded content**

Look for:
- Strings in JSX that should be editable
- Placeholder text that wasn't replaced
- CTAs with hardcoded labels
- Section titles not from CMS

**Red flags:**
```tsx
// BAD: Hardcoded
<h1>Welcome to Our Company</h1>
<button>Get Started</button>

// GOOD: From CMS
<h1>{data.hero.headline}</h1>
<button>{data.hero.ctaText}</button>
```

### Phase 4: Generate Gap Report

Create a report with three sections:

**1. Fields Not Queried**
Schema fields that exist but aren't in any query.

```markdown
### Fields Not Queried

| Type | Field | Recommendation |
|------|-------|----------------|
| homepage | testimonials | Add to HOMEPAGE_QUERY |
| service | duration | Add to SERVICE_QUERY if needed |
```

**2. Fields Not Rendered**
Queried fields that aren't used in any component.

```markdown
### Fields Not Rendered

| Type | Field | Query | Recommendation |
|------|-------|-------|----------------|
| homepage | hero.ctaLink | HOMEPAGE_QUERY | Use in Hero component |
| post | author.bio | POST_QUERY | Display or remove from query |
```

**3. Hardcoded Content**
Text that should be CMS-driven but isn't.

```markdown
### Hardcoded Content

| File | Line | Content | Recommendation |
|------|------|---------|----------------|
| components/Hero.tsx | 24 | "Learn More" | Add ctaText to homepage schema |
| components/Footer.tsx | 45 | "© 2024 Company" | Add copyrightText to footer settings |
| app/about/page.tsx | 12 | "Our Mission" | Add sectionTitle to aboutPage schema |
```

### Phase 5: Fix and Verify

**For each gap identified:**

1. **If field missing from query:** Add to GROQ projection
2. **If field not rendered:** Add to component OR remove from query
3. **If hardcoded:** Add schema field, query it, use in component

**Verification checklist per fix:**
- [ ] Field appears in Sanity studio
- [ ] Field value shows on site
- [ ] Editing field in Sanity updates site

## Anti-Patterns

### "I'll Add It Later"
- **Don't** leave TODO comments for CMS fields
- **Do** add fields completely (schema + query + component) or not at all

### Hardcoding "Temporary" Values
- **Don't** hardcode section titles, CTAs, or any client-facing text
- **Do** add schema fields even for "obvious" content like "About Us"

### Skipping Singleton Pages
- **Don't** forget homepage, about page, footer, etc.
- **Do** create singleton schemas for EVERY page with editable content

### Partial Field Implementation
- **Don't** add fields to schema without adding to query
- **Do** implement the full chain: schema → query → component

### Over-Fetching in Queries
- **Don't** query fields you don't render (wastes bandwidth)
- **Do** keep queries minimal and aligned with component needs

## Common Content That Gets Missed

### Homepage
- [ ] Hero headline and subheadline
- [ ] Hero CTA text and link
- [ ] Hero background image
- [ ] Section titles ("Our Services", "What Clients Say")
- [ ] Any "Featured" or "Selected" content

### Footer
- [ ] Copyright text
- [ ] Company description
- [ ] Social media links
- [ ] Navigation links
- [ ] Contact information

### About Page
- [ ] Page title
- [ ] Mission/vision statements
- [ ] Team member bios
- [ ] Company history
- [ ] Values/principles

### Services/Products
- [ ] Service title and description
- [ ] Pricing information
- [ ] Duration/timeline
- [ ] Features list
- [ ] Call-to-action

### Contact Page
- [ ] Page intro text
- [ ] Address
- [ ] Phone number
- [ ] Email
- [ ] Business hours
- [ ] Form labels and placeholder text

## Verification Commands

**Find schema field definitions:**
```bash
# List all defineField calls
grep -r "defineField" sanity/schemas/ --include="*.ts"

# List all field names
grep -r "name:" sanity/schemas/ --include="*.ts" | grep -v "_type"
```

**Find GROQ queries:**
```bash
# Find query definitions
grep -r "groq\`" --include="*.ts" --include="*.tsx"

# Find sanityFetch calls
grep -r "sanityFetch" --include="*.ts" --include="*.tsx"
```

**Find potential hardcoded text:**
```bash
# Find string literals in JSX (rough heuristic)
grep -r ">[A-Z][a-z]" components/ --include="*.tsx" | head -50
```

## Report Template

```markdown
# CMS Verification Report

**Project:** [Project Name]
**Date:** [Date]
**Status:** [Pass/Fail]

## Summary

| Category | Count | Status |
|----------|-------|--------|
| Schema fields | XX | - |
| Queried fields | XX | - |
| Rendered fields | XX | - |
| Gaps found | XX | ❌/✅ |

## Gaps Identified

### Critical (Must Fix Before Handoff)
1. [Gap description and fix]
2. [Gap description and fix]

### Important (Should Fix)
1. [Gap description and fix]

### Nice to Have (Low Priority)
1. [Gap description and fix]

## Hardcoded Content Found
[List with file, line, content, recommendation]

## Recommendations
1. [Recommendation]
2. [Recommendation]

## Verification Checklist
- [ ] All schema fields are queried (or intentionally excluded)
- [ ] All queried fields are rendered
- [ ] No hardcoded user-facing text remains
- [ ] Singleton pages have complete field coverage
- [ ] Changes tested in Sanity studio
```

## Quality Checklist

Before declaring CMS verification complete:

- [ ] **Schema inventory complete** - All document and singleton types cataloged
- [ ] **Query inventory complete** - All GROQ queries mapped
- [ ] **Component inventory complete** - All CMS-consuming components checked
- [ ] **Gap report generated** - Missing fields documented
- [ ] **Hardcoded content identified** - All instances listed
- [ ] **Fixes applied** - Gaps closed with complete implementation
- [ ] **Verification passed** - Each field editable in Sanity, visible on site

## Integration with Other Skills

**Depends on:**
- `sanity-setup` - CMS must be properly configured first

**Followed by:**
- `performance-audit` - Run after CMS is complete

**Run this skill:**
- Before client handoff
- After adding new features with CMS content
- When client reports "can't edit" issues

## Example Workflow

```
1. User: "Verify CMS for client handoff"

2. Claude inventories:
   - Reads all schema files
   - Finds all GROQ queries
   - Traces component data usage

3. Claude identifies gaps:
   - footer.socialLinks not in query
   - aboutPage.teamPhoto hardcoded
   - homepage.testimonials queried but not rendered

4. Claude generates report:
   - 3 critical gaps
   - 2 hardcoded items
   - Specific fixes for each

5. Claude fixes:
   - Adds socialLinks to footer query
   - Adds teamPhoto to aboutPage schema
   - Removes unused testimonials from query

6. Claude verifies:
   - Tests each fix in Sanity
   - Confirms changes appear on site
```

## Success Criteria

**CMS verification is complete when:**
1. Every user-facing text is editable in Sanity
2. Zero hardcoded content that should be CMS-driven
3. All queried fields are actually rendered
4. Client can edit any text they see on the site

**Measure success by:** Can the client change EVERY piece of text on the site without developer help? If not, CMS is incomplete.
