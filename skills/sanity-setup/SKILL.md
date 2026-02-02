---
name: sanity-setup
description: Configure Sanity CMS with presentation mode, embedded studio, and Vercel deployment—correctly the first time. Eliminates the iteration pain of debugging CMS integration.
triggers:
  keywords:
    - "set up Sanity"
    - "add CMS"
    - "configure presentation mode"
    - "Sanity studio"
    - "sanity init"
    - "add content management"
    - "headless CMS"
    - "visual editing"
    - "draft mode"
    - "preview mode"
  contexts:
    - "Starting a new project that needs CMS"
    - "Adding CMS to existing Next.js project"
    - "Debugging Sanity presentation mode issues"
    - "Setting up preview/draft mode"
    - "Deploying Sanity project to Vercel"
  prerequisites:
    - "Next.js 13+ project with App Router"
    - "Sanity account (free tier works)"
    - "Basic understanding of desired content types"
  anti_triggers:
    - "Sanity already fully configured and working"
    - "Using a different CMS (Contentful, Strapi, etc.)"
    - "Static site without CMS needs"
    - "Just adding a new schema field (not full setup)"
---

# Sanity Setup

This skill guides Claude through configuring Sanity CMS with all modern features—presentation mode, embedded studio, draft mode, and Vercel deployment—so it works correctly the first time.

## Why This Matters

**The Problem:**
Sanity setup has many moving parts that must work together:
- Project initialization and configuration
- Studio embedding in Next.js
- CORS configuration
- Presentation mode (visual editing)
- Draft mode API routes
- Environment variables
- Vercel deployment webhooks

Missing any piece means debugging. Getting it right the first time saves hours.

**The Solution:**
Follow a structured checklist that verifies each step before moving on.

## Core Methodology

### Order Matters

The setup must happen in this order:
1. **Initialize Sanity project** (get project ID)
2. **Configure sanity.config.ts** (studio configuration)
3. **Create studio route** (embed in Next.js)
4. **Set up CORS** (allow localhost + production)
5. **Configure draft mode** (API routes + data fetching)
6. **Set up presentation mode** (visual editing)
7. **Configure environment variables** (local + Vercel)
8. **Set up revalidation webhook** (for ISR)
9. **Verify everything works** (test each checkpoint)

## Process

### Phase 1: Initialize Sanity Project

**Option A: New Sanity Project**
```bash
npm create sanity@latest -- --project-id <id> --dataset production
```

**Option B: Use Existing Project**
Get project ID from: https://www.sanity.io/manage

**Required Information:**
- Project ID (from Sanity dashboard)
- Dataset name (usually "production")
- API version (use current date: `2024-01-01`)

**Verification:**
- [ ] Have project ID
- [ ] Have dataset name
- [ ] Project visible in Sanity dashboard

### Phase 2: Install Dependencies

```bash
npm install sanity @sanity/vision @sanity/image-url @sanity/client next-sanity
npm install -D @sanity/eslint-config-studio
```

**Optional but recommended:**
```bash
npm install @sanity/presentation
```

### Phase 3: Create Configuration Files

**File 1: `sanity.config.ts`** (project root)

See template: `templates/sanity.config.ts.template`

Key elements:
- Project ID from env var
- Dataset from env var
- Plugins: structureTool, visionTool, presentationTool
- Schema types

**File 2: `sanity/env.ts`**

See template: `templates/env.ts.template`

Central place for Sanity environment variables.

**File 3: `sanity/lib/client.ts`**

See template: `templates/client.ts.template`

Sanity client for data fetching.

**File 4: `sanity/lib/queries.ts`**

See template: `templates/queries.ts.template`

Centralized GROQ queries.

### Phase 4: Embed Studio in Next.js

**Create route: `app/studio/[[...tool]]/page.tsx`**

See template: `templates/studio-route.tsx.template`

**Important:** The catch-all `[[...tool]]` allows deep linking into studio tools.

**Verification:**
- [ ] Visit `/studio` - studio loads
- [ ] Can log into Sanity
- [ ] Can navigate studio without errors

### Phase 5: Configure CORS

In Sanity dashboard (https://www.sanity.io/manage):

1. Go to project → API → CORS origins
2. Add:
   - `http://localhost:3000` (for development)
   - `https://your-domain.com` (for production)
   - `https://*.vercel.app` (for preview deployments)

**Allow credentials** for each origin (required for authentication).

**Verification:**
- [ ] Studio loads without CORS errors in browser console
- [ ] Can save content in embedded studio

### Phase 6: Set Up Draft Mode

Draft mode enables previewing unpublished content.

**File 1: `app/api/draft-mode/enable/route.ts`**

See template: `templates/draft-mode-enable.ts.template`

**File 2: `app/api/draft-mode/disable/route.ts`**

See template: `templates/draft-mode-disable.ts.template`

**File 3: Update data fetching pattern**

```typescript
// In your data fetching utility
import { draftMode } from "next/headers";

export async function sanityFetch<T>({ query, params = {} }: SanityFetchParams): Promise<T> {
  const { isEnabled } = await draftMode();

  const perspective = isEnabled ? "previewDrafts" : "published";

  return client.fetch<T>(query, params, {
    perspective,
    stega: isEnabled, // Enables click-to-edit
  });
}
```

**Verification:**
- [ ] Visit `/api/draft-mode/enable` - redirects and shows draft content
- [ ] Visit `/api/draft-mode/disable` - shows published content
- [ ] Unpublished changes visible in draft mode

### Phase 7: Configure Presentation Mode

Presentation mode enables visual editing—clicking on content to edit it.

**Update `sanity.config.ts`:**

```typescript
import { presentationTool } from "@sanity/presentation";

plugins: [
  presentationTool({
    previewUrl: {
      origin: process.env.NEXT_PUBLIC_SITE_URL || "http://localhost:3000",
      draftMode: {
        enable: "/api/draft-mode/enable",
      },
    },
  }),
  // other plugins...
]
```

**Verification:**
- [ ] In studio, "Presentation" tab appears
- [ ] Preview loads your site
- [ ] Clicking text shows edit overlay
- [ ] Changes in studio appear in preview

### Phase 8: Environment Variables

**Local (`.env.local`):**
```
NEXT_PUBLIC_SANITY_PROJECT_ID=your-project-id
NEXT_PUBLIC_SANITY_DATASET=production
NEXT_PUBLIC_SANITY_API_VERSION=2024-01-01
SANITY_API_TOKEN=your-read-token
SANITY_REVALIDATE_SECRET=your-random-secret
NEXT_PUBLIC_SITE_URL=http://localhost:3000
```

**Vercel Environment Variables:**

| Variable | Value | Environment |
|----------|-------|-------------|
| `NEXT_PUBLIC_SANITY_PROJECT_ID` | your-project-id | All |
| `NEXT_PUBLIC_SANITY_DATASET` | production | All |
| `NEXT_PUBLIC_SANITY_API_VERSION` | 2024-01-01 | All |
| `SANITY_API_TOKEN` | (from Sanity) | All |
| `SANITY_REVALIDATE_SECRET` | (random string) | All |
| `NEXT_PUBLIC_SITE_URL` | https://your-domain.com | Production |
| `NEXT_PUBLIC_SITE_URL` | (auto) | Preview |

**Getting the API Token:**
1. Sanity dashboard → Project → API → Tokens
2. Create new token with "Viewer" permission
3. Copy token value

**Verification:**
- [ ] `env.local` has all variables
- [ ] Vercel has all variables configured
- [ ] No `undefined` errors in console

### Phase 9: Set Up Revalidation Webhook

For ISR (Incremental Static Regeneration) to work, Sanity must notify Next.js when content changes.

**File: `app/api/revalidate/route.ts`**

See template: `templates/revalidate.ts.template`

**Configure webhook in Sanity:**
1. Sanity dashboard → Project → API → Webhooks
2. Create webhook:
   - Name: "Revalidate Production"
   - URL: `https://your-domain.com/api/revalidate?secret=YOUR_SECRET`
   - Dataset: production
   - Trigger on: Create, Update, Delete
   - Filter: (leave blank for all, or specify document types)

**Verification:**
- [ ] Edit content in Sanity
- [ ] Publish changes
- [ ] Site updates without redeploy (check network tab)

## Anti-Patterns

### Configuration

- **Don't hardcode project ID** - Always use env vars
- **Don't skip CORS configuration** - Studio won't load without it
- **Don't forget preview deployment CORS** - Add `*.vercel.app`
- **Don't use deprecated `preview` mode** - Use `draftMode()` (Next.js 13+)

### API Tokens

- **Don't commit tokens to git** - Always use env vars
- **Don't use write tokens for read-only** - Use minimal permissions
- **Don't share tokens between environments** - Create separate tokens

### Data Fetching

- **Don't fetch without perspective** - Always specify published/previewDrafts
- **Don't skip stega for presentation mode** - Required for click-to-edit
- **Don't forget revalidation** - Set appropriate cache times

## File Templates

### sanity.config.ts
Location: `~/.claude/skills/sanity-setup/templates/sanity.config.ts.template`

### env.ts
Location: `~/.claude/skills/sanity-setup/templates/env.ts.template`

### client.ts
Location: `~/.claude/skills/sanity-setup/templates/client.ts.template`

### studio-route.tsx
Location: `~/.claude/skills/sanity-setup/templates/studio-route.tsx.template`

### draft-mode-enable.ts
Location: `~/.claude/skills/sanity-setup/templates/draft-mode-enable.ts.template`

### draft-mode-disable.ts
Location: `~/.claude/skills/sanity-setup/templates/draft-mode-disable.ts.template`

### sanity-fetch.ts
Location: `~/.claude/skills/sanity-setup/templates/sanity-fetch.ts.template`

### revalidate.ts
Location: `~/.claude/skills/sanity-setup/templates/revalidate.ts.template`

### env-checklist.md
Location: `~/.claude/skills/sanity-setup/templates/env-checklist.md`

## Quality Checklist

Before declaring Sanity setup complete:

### Core Functionality
- [ ] **Studio loads** at `/studio`
- [ ] **Can create content** in studio
- [ ] **Can edit content** in studio
- [ ] **Content appears** on site

### Preview/Draft Mode
- [ ] **Draft mode enables** via API route
- [ ] **Unpublished content visible** in draft mode
- [ ] **Published content only** in production mode
- [ ] **Draft mode disables** correctly

### Presentation Mode
- [ ] **Presentation tab** appears in studio
- [ ] **Preview loads** in presentation mode
- [ ] **Click-to-edit works** on text
- [ ] **Changes appear** in preview without refresh

### Deployment
- [ ] **Environment variables** configured in Vercel
- [ ] **CORS configured** for production domain
- [ ] **Webhook configured** for revalidation
- [ ] **Content updates** appear without redeploy

## Common Issues & Solutions

### "CORS policy blocked"
**Cause:** Origin not added to CORS in Sanity dashboard
**Fix:** Add origin with credentials enabled

### "Studio not loading"
**Cause:** Missing catch-all route or wrong path
**Fix:** Ensure route is `app/studio/[[...tool]]/page.tsx`

### "Draft mode not working"
**Cause:** Missing API token or wrong perspective
**Fix:** Check SANITY_API_TOKEN and sanityFetch implementation

### "Presentation mode not clicking"
**Cause:** Missing stega in data fetching
**Fix:** Add `stega: true` when draft mode enabled

### "Content not updating on publish"
**Cause:** Webhook not configured or wrong secret
**Fix:** Check webhook URL and secret match

## Integration with Other Skills

**Depends on:**
- `design-system-init` - Studio can use brand colors for theming

**Followed by:**
- `cms-verify` - Verify all fields flow through correctly
- Component development - Build components using CMS data

## Example Workflow

```
1. User: "Set up Sanity for this project"

2. Claude asks:
   - Do you have a Sanity project ID, or should we create new?
   - What content types do you need? (pages, posts, services, etc.)
   - Will you need presentation mode (visual editing)?

3. Claude executes:
   - Install dependencies
   - Create configuration files
   - Set up studio route
   - Configure draft mode
   - Set up presentation mode
   - Create env checklist

4. Claude verifies:
   - Studio loads
   - Can create test content
   - Draft mode works
   - Presentation mode works

5. Claude provides:
   - Environment variable checklist for Vercel
   - Webhook setup instructions
   - Next steps for schema development
```

## Success Criteria

**Sanity setup is complete when:**
1. Studio loads at `/studio` without errors
2. Content can be created and edited
3. Draft mode shows unpublished content
4. Presentation mode enables click-to-edit
5. Published content appears on live site
6. Content updates without redeploy (ISR working)

**Measure success by:** Can a non-technical user edit content and see changes on the live site without developer intervention?
