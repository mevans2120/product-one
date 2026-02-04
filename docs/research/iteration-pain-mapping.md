# Iteration Pain Mapping

**Date:** 2026-02-01
**Purpose:** Identify where agent iteration stalls to inform tooling/workflow improvements

---

## Identified Pain Points

### 1. Design System Setup (Early Project)

**Problem:** When a project is new, the design system gets set up in a "willy nilly fashion" without clear direction.

**Symptoms:**
- Inconsistent color usage (hardcoded vs variables)
- Typography scale not defined upfront
- Spacing/sizing without system
- Components styled ad-hoc rather than from tokens

**Why it causes iteration:**
- Later changes require touching many files
- Agent doesn't know the "right" way without guidance
- Refactoring compounds as project grows

**Potential solutions:**
- [ ] Design system template with pre-defined structure (globals.css, tailwind.config)
- [ ] Checklist/prompt for design decisions before coding starts
- [ ] Specialized "design system setup" agent or phase

---

### 2. Sanity Implementation

**Problem:** Multiple friction points with Sanity CMS setup.

#### 2a. Presentation Mode Setup

**What it is:** Sanity's visual editing feature that allows clicking on the preview to edit content.

**Why it's painful:**
- Requires specific configuration (draftMode, perspective, studioUrl)
- Integration with Next.js App Router has gotchas
- Documentation is spread across Sanity + Next.js

**Questions to clarify:**
- What specifically goes wrong? (Configuration? Routing? Visual editor not connecting?)
- Is this a "do it once, copy the pattern" problem?

#### 2b. Vercel CLI Integration

**What it is:** Deploying, environment variables, preview deployments.

**Why it's painful:**
- Environment variable setup for Sanity tokens
- Preview vs production configuration
- CLI commands for specific tasks

**Questions to clarify:**
- Which CLI operations cause friction?
- Is this about knowing the right commands, or about debugging deployment issues?

#### 2c. CMS Field Editability

**Problem:** Not all fields are editable in the CMS at first—requires 1-2 iterations.

**Why it happens:**
- Schema defines fields but component doesn't use them
- Or component uses hardcoded values instead of CMS data
- Or field exists but isn't exposed in Sanity's desk structure

**Pattern observed:** This seems like a "completeness check" problem—ensuring schema → query → component are all connected.

**Potential solutions:**
- [ ] Schema-to-component checklist
- [ ] Automated check: "fields in schema but not in query"
- [ ] Standard pattern for connecting all three layers

---

### 3. Performance / Lighthouse

**Problem:** Getting good Lighthouse scores requires optimization iterations.

**Common issues:**
- Image optimization (sizing, formats, lazy loading)
- Font loading strategy (preload, font-display)
- JavaScript bundle size
- Core Web Vitals (LCP, CLS, FID)

**Why it causes iteration:**
- Lighthouse runs after build
- Each fix requires rebuild + retest
- Some fixes conflict (e.g., preloading everything hurts other metrics)

**Questions to clarify:**
- Which Lighthouse categories cause most friction? (Performance? Accessibility? SEO?)
- Is this about knowing what to fix, or about the fix-test-fix cycle?

**Potential solutions:**
- [ ] Pre-flight Lighthouse check before "done"
- [ ] Template with performance best practices baked in
- [ ] Specialized "performance audit" agent

---

## Pain Point Priority Matrix

| Pain Point | Frequency | Time Cost | Solvable with Template? | Needs Specialized Agent? |
|------------|-----------|-----------|-------------------------|--------------------------|
| Design System Setup | Every project | High | Yes (mostly) | Maybe |
| Sanity Presentation Mode | Every project | Medium | Yes (pattern) | No |
| Vercel CLI | Every project | Low-Medium | Yes (docs) | No |
| CMS Field Editability | Every project | Medium | Partial | Maybe (validation) |
| Lighthouse Performance | Every project | Medium-High | Partial | Maybe |

---

## Emerging Patterns

### Template Solutions (Low-Hanging Fruit)

Things that can be solved with better starting points:

1. **Design system skeleton** - Pre-defined CSS variables, Tailwind config, typography scale
2. **Sanity presentation mode boilerplate** - Copy-paste configuration
3. **Vercel deployment checklist** - Environment variables, commands

### Process Solutions (Workflow Changes)

Things that need checkpoints or validation:

1. **Schema-query-component check** - Before "CMS done", verify all fields flow through
2. **Performance check** - Before "done", run Lighthouse and address issues
3. **Design system review** - Before building components, confirm tokens are defined

### Agent Solutions (Specialized Roles)

Things that might benefit from specialized agents:

1. **Design system agent** - Sets up tokens, ensures consistency
2. **CMS validation agent** - Checks schema → query → component completeness
3. **Performance agent** - Runs Lighthouse, suggests fixes, verifies improvements

---

## Questions for Further Mapping

1. **Sanity presentation mode:** What specifically goes wrong? Is it configuration, or debugging why it's not working?

2. **Vercel CLI:** Which operations? Deployment? Environment setup? Something else?

3. **Lighthouse:** Which categories cause most friction—Performance, Accessibility, SEO, Best Practices?

4. **Design system:** Would a structured template (with blanks to fill in) help? Or is the problem that design decisions aren't made upfront?

5. **CMS fields:** Is this about forgetting to add fields, or about the back-and-forth to get them "right"?

---

## Skills as a Solution Pattern

The `design-concepts` skill works well because it:

1. **Forces a structured process** - Can't skip steps
2. **Has clear outputs** - Know what "done" looks like
3. **Includes anti-patterns** - Explicit guidance on what NOT to do
4. **Has quality checklists** - Validation before delivery
5. **Organizes files consistently** - Predictable naming and location

### Potential Skills to Create

| Pain Point | Skill Name | Purpose |
|------------|------------|---------|
| Design System Setup | `design-system-init` | Establish tokens, colors, typography BEFORE coding |
| Sanity Presentation Mode | `sanity-setup` | Step-by-step configuration with verification |
| CMS Field Completeness | `cms-verify` | Check schema → query → component flow |
| Lighthouse Performance | `performance-audit` | Run Lighthouse, systematic fixes, verify improvements |

### Skill Template Structure

Based on what works in `design-concepts`:

```markdown
---
name: skill-name
description: What this skill does
triggers:
  keywords: [when to activate]
  prerequisites: [what must be true first]
  anti_triggers: [when NOT to use]
---

# Skill Name

## Purpose
Why this skill exists, what problem it solves

## Process
1. Step one (with verification)
2. Step two (with verification)
3. ...

## Anti-Patterns
- ❌ Don't do X (why)
- ❌ Don't do Y (why)

## Quality Checklist
- [ ] Verification 1
- [ ] Verification 2

## File Organization
Where outputs go, naming conventions
```

### Skills vs. Agents

**Skills** work well for:
- Structured processes with clear steps
- Single-agent tasks with checkpoints
- Reusable patterns across projects

**Multi-agent** might work better for:
- Parallel independent tasks
- Specialized expertise (design vs. performance vs. CMS)
- Long-running tasks that benefit from fresh context

**Hybrid approach:**
- Skills define the process
- Agents execute specialized portions
- Orchestrator manages handoffs

---

## Next Steps

1. ~~Answer clarifying questions above~~
2. Prioritize which pain points to solve first
3. **Prototype a skill for the highest-priority pain point**
4. Test skill on next project
5. Consider multi-agent only where skills aren't sufficient
