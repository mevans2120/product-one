---
name: design-system-init
description: Establishes design tokens, colors, typography, and spacing BEFORE any component code is written. Prevents downstream iteration by creating a complete, consistent design foundation at project start.
triggers:
  keywords:
    - "new project"
    - "set up design system"
    - "initialize styles"
    - "design tokens"
    - "set up tailwind"
    - "configure colors"
    - "typography setup"
    - "fresh next.js project"
    - "start new site"
    - "design foundation"
    - "css variables"
    - "theme setup"
  contexts:
    - "Starting a fresh Next.js + Tailwind project"
    - "Client onboarding with brand guidelines"
    - "Before any component development begins"
    - "Migrating to a new design system"
    - "Setting up a project template"
  prerequisites:
    - "Next.js project initialized"
    - "Tailwind CSS installed"
    - "Brand colors OR aesthetic direction available"
  anti_triggers:
    - "Components already built (too late for init)"
    - "Refactoring existing design system (use different approach)"
    - "Just changing one color (simple edit)"
    - "No Tailwind in project"
---

# Design System Init

This skill guides Claude through establishing a complete design system foundation BEFORE any component code is written. A properly initialized design system prevents the "willy-nilly" styling that causes iteration pain downstream.

## Why This Matters

**The Problem:**
When design systems are set up ad-hoc during development:
- Hardcoded colors scattered across components
- Inconsistent typography (font sizes, weights, line heights)
- No spacing scale (random px/rem values everywhere)
- Dark mode added later causes massive refactoring
- Every new component requires design decisions

**The Solution:**
Invest 15-30 minutes upfront to establish ALL design tokens before writing component code. This creates:
- Single source of truth for all visual properties
- Zero design decisions during component development
- Dark mode ready from day one
- Consistent, professional aesthetic

## Core Methodology

### Design System First, Components Second

The order matters:
1. **Gather brand inputs** (colors, fonts, mood)
2. **Generate design tokens** (CSS variables, Tailwind config)
3. **Create foundational files** (globals.css, fonts.ts)
4. **Verify with sample component** (ensure tokens work)
5. **THEN start building components**

Never skip to components. Never add tokens "as you go."

## Process

### Phase 1: Gather Brand Inputs

**Ask the user these questions:**

1. **Colors:**
   - Primary brand color? (hex, or describe: "forest green")
   - Secondary color? (optional)
   - Accent color for CTAs? (optional)
   - Any colors to avoid?

2. **Typography:**
   - Serif or sans-serif preference?
   - Mood? (elegant, modern, playful, corporate, etc.)
   - Any specific fonts in brand guidelines?

3. **Reference:**
   - Any websites with the right "feel"?
   - Industry/vertical? (spa, law firm, creative agency, etc.)

4. **Dark mode:**
   - Required? Likely in future? Can skip entirely?

**If user provides minimal input:**
Make informed decisions based on industry/mood, but document assumptions clearly.

### Phase 2: Generate Design Tokens

**Color System (HSL Format):**

Use HSL for better color manipulation:
```css
/* Example: Warm spa aesthetic */
--color-primary: 158 45% 35%;      /* Sage green */
--color-secondary: 30 55% 92%;      /* Warm cream */
--color-accent: 18 65% 55%;         /* Terracotta */
--color-background: 40 30% 97%;     /* Off-white */
--color-foreground: 20 10% 15%;     /* Warm dark */
```

**Typography Scale:**

Use a consistent scale (1.25 or 1.333 ratio):
```css
--font-size-xs: 0.75rem;    /* 12px */
--font-size-sm: 0.875rem;   /* 14px */
--font-size-base: 1rem;     /* 16px */
--font-size-lg: 1.125rem;   /* 18px */
--font-size-xl: 1.25rem;    /* 20px */
--font-size-2xl: 1.5rem;    /* 24px */
--font-size-3xl: 1.875rem;  /* 30px */
--font-size-4xl: 2.25rem;   /* 36px */
--font-size-5xl: 3rem;      /* 48px */
```

**Spacing Scale:**

Use Tailwind's default or customize:
```css
/* Only define if deviating from Tailwind defaults */
--spacing-section: 5rem;    /* Between page sections */
--spacing-container: 1.5rem; /* Container padding */
```

### Phase 3: Create Foundational Files

**File 1: `app/globals.css`**

See template: `templates/globals.css.template`

Key sections:
1. CSS reset/base (Tailwind layers)
2. CSS variables (light mode)
3. Dark mode variables
4. Typography base styles
5. Custom utility classes (if needed)

**File 2: `tailwind.config.ts`**

See template: `templates/tailwind.config.ts.template`

Key extensions:
1. Colors referencing CSS variables
2. Font family definitions
3. Any custom spacing or sizing

**File 3: `lib/fonts.ts`**

See template: `templates/fonts.ts.template`

Key elements:
1. next/font imports
2. Variable font configuration
3. CSS variable assignment

### Phase 4: Verification

Before declaring "done," verify with a sample component:

```tsx
// Test component - delete after verification
export function DesignSystemTest() {
  return (
    <div className="bg-background text-foreground p-8">
      <h1 className="font-heading text-4xl text-primary mb-4">
        Heading Test
      </h1>
      <p className="font-body text-lg text-muted-foreground mb-6">
        Body text with muted color. This should look balanced.
      </p>
      <button className="bg-primary text-primary-foreground px-6 py-3 rounded-lg hover:bg-primary/90">
        CTA Button
      </button>
      <div className="mt-8 p-4 bg-secondary rounded-lg">
        <p className="text-secondary-foreground">
          Secondary section background
        </p>
      </div>
    </div>
  );
}
```

**Verification Checklist:**
- [ ] All colors render as expected
- [ ] Typography scale looks balanced
- [ ] Dark mode works (if applicable)
- [ ] No hardcoded hex/rgb values in test component
- [ ] Fonts load via next/font (check Network tab)
- [ ] No console errors about missing CSS variables

## Anti-Patterns

### Typography

**Avoid these overused fonts:**
- Inter (extremely overused, "AI slop" aesthetic)
- Roboto
- Arial
- Open Sans
- System fonts (unless specifically appropriate)

**Use distinctive alternatives:**
- **Serif elegance:** Fraunces, Newsreader, Lora, Crimson Pro
- **Display impact:** Cabinet Grotesk, Clash Display, General Sans
- **Clean sans-serif:** Satoshi, Syne, Manrope, DM Sans, Outfit
- **Technical feel:** JetBrains Mono, IBM Plex Mono

### Colors

**Avoid cliched schemes:**
- Purple gradients on white (overused)
- Generic blue + gray
- Pastel everything (unless contextually appropriate)
- Rainbow with equal distribution

**Strong approaches:**
- Dominant + accent (80/20 rule)
- Dark with neon accents
- Monochrome + single hue
- Cultural aesthetics (Brutalism, Memphis, Bauhaus)

### Process

- **Don't hardcode colors in components** - Always use CSS variables
- **Don't skip dark mode variables** - Add them even if not used initially
- **Don't define tokens piecemeal** - Complete the system upfront
- **Don't use Inter** - Find a distinctive alternative
- **Don't skip verification** - Test before building components

## File Templates

### globals.css

Location: `~/.claude/skills/design-system-init/templates/globals.css.template`

### tailwind.config.ts

Location: `~/.claude/skills/design-system-init/templates/tailwind.config.ts.template`

### fonts.ts

Location: `~/.claude/skills/design-system-init/templates/fonts.ts.template`

## Quality Checklist

Before completing design system initialization:

- [ ] **Colors defined** - Primary, secondary, accent, background, foreground as CSS variables
- [ ] **Dark mode ready** - Variables defined in `.dark` selector (even if not used)
- [ ] **Typography scale** - Font sizes documented and consistent
- [ ] **Fonts configured** - Using next/font, NOT external CDN links
- [ ] **Tailwind extended** - Config references CSS variables
- [ ] **No hardcoded values** - Test component uses only token classes
- [ ] **Sample renders correctly** - Visual verification passed
- [ ] **Documentation** - Assumptions and decisions noted (in code comments)

## Integration with Other Skills

**This skill comes FIRST.** Other skills depend on design tokens being established:

- **Before components:** Design system enables consistent component development
- **Before Sanity setup:** Studio theming can use brand colors
- **Before performance audit:** Fonts properly loaded via next/font from start

## Common Scenarios

### Scenario 1: Client with Brand Guidelines

```
User: "Set up design system for a law firm. They use navy blue (#1a365d) and gold (#d69e2e)."

Process:
1. Convert hex to HSL for CSS variables
2. Derive complementary colors (backgrounds, text)
3. Suggest professional serif font (Newsreader, Lora)
4. Create conservative, trust-building aesthetic
5. Generate all files
6. Verify with sample component
```

### Scenario 2: Minimal Input

```
User: "New spa website, warm and calming feel."

Process:
1. Derive palette: sage green, warm cream, terracotta accents
2. Suggest elegant serif (Fraunces) + clean sans (DM Sans)
3. Assume dark mode NOT needed (spa = light, airy)
4. Document assumptions clearly
5. Generate all files
6. Verify with sample component
```

### Scenario 3: Dark-First Design

```
User: "Portfolio site for a developer. Dark theme, modern."

Process:
1. Start with dark backgrounds, light text
2. Neon or vibrant accent colors
3. Monospace for code, modern sans for UI
4. Light mode as secondary (invert the variables)
5. Generate all files
6. Verify with sample component
```

## Example Output

After running this skill, the project should have:

```
app/
├── globals.css          # CSS variables, base styles
├── layout.tsx           # Imports fonts.ts
lib/
├── fonts.ts             # next/font configuration
tailwind.config.ts       # Extended with design tokens
```

And component development can begin with confidence that:
- All colors reference `bg-primary`, `text-foreground`, etc.
- All fonts use `font-heading`, `font-body`
- No hardcoded values needed
- Dark mode works without changes

## Success Criteria

**Design system init is complete when:**
1. Zero hardcoded colors in subsequent development
2. All tokens documented in CSS variables
3. Sample component renders correctly
4. Dark mode variables exist (even if unused)
5. Fonts load via next/font

**Measure success by:** Does the developer need to make ANY design decisions while building components? If yes, the design system is incomplete.
