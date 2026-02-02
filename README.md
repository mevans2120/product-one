# Product One

Skills and research for building small business websites faster using AI agents.

## Skills

Reusable Claude Code skills for the website development workflow:

| Skill | Purpose | When to Use |
|-------|---------|-------------|
| `site-audit` | Audit existing sites before redesign | Start of redesign projects |
| `design-system-init` | Establish design tokens before coding | Start of any project |
| `sanity-setup` | Configure Sanity CMS correctly | After design system |
| `cms-verify` | Verify schema → query → component flow | Before client handoff |
| `performance-audit` | Systematic Lighthouse optimization | Before launch |

### Installation

Copy skills to your Claude Code skills directory:

```bash
cp -r skills/* ~/.claude/skills/
```

Or symlink for development:

```bash
ln -s $(pwd)/skills/* ~/.claude/skills/
```

## Workflow

### New Project
```
design-system-init → [build] → sanity-setup → [content] → cms-verify → [polish] → performance-audit
```

### Redesign Project
```
site-audit → design-system-init → [build] → sanity-setup → [migrate] → cms-verify → performance-audit
```

## Research

Project analysis and research documents:

- `projects-comparison.md` - Analysis of 4 existing projects
- `shadcn-usage-audit.md` - Component library usage patterns
- `iteration-pain-mapping.md` - Where agent iteration stalls
- `skills-implementation-plan.md` - Implementation roadmap

## Tech Stack

Standard stack for all projects:

- **Framework:** Next.js 15 (App Router)
- **Language:** TypeScript
- **Styling:** Tailwind CSS
- **CMS:** Sanity
- **Forms:** React Hook Form + Zod
- **eCommerce:** Lemon Squeezy (digital goods)
- **Booking:** Calendly
- **Deployment:** Vercel
