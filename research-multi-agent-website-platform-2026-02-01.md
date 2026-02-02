# Multi-Agent Website Building Platform Research

**Date:** 2026-02-01
**Status:** Initial Research
**Focus:** Platform strategy for accelerating small business website creation using multiple AI agents

## Executive Summary

You've validated a valuable service: AI-built small business websites delivered in days instead of weeks. The next step is building infrastructure to scale this—but the question is *what* to build. This research explores three strategic directions, the research needed to choose between them, and a concrete path to pilot and build.

**Key Finding:** Before building any platform, you need to codify what you've learned. Your current process—whatever it is—contains implicit knowledge that must become explicit before it can be automated or multiplied.

---

## Part 1: Where to Start

### The Fundamental Question

You're solving three different problems that require different solutions:

| Problem | Solution Type | Build Complexity |
|---------|---------------|------------------|
| "I want to build sites faster" | Personal tooling & workflows | Low |
| "I want multiple agents working together" | Orchestration system | Medium-High |
| "I want others to use this system" | Platform/product | High |

**Recommendation:** Start with Problem 1, validate with Problem 2, then decide on Problem 3.

### Where You Actually Are

Before building anything, answer these questions:

1. **What's your current process?** Document every step from client intake to site delivery
2. **Where do you lose time?** Is it context-switching, repetitive tasks, waiting, or rework?
3. **What decisions require your judgment?** These are hard to automate
4. **What's mechanical?** These are easy to automate

### The Starting Point Checklist

- [ ] Document your current end-to-end workflow
- [ ] Time-track your next 2-3 projects to find bottlenecks
- [ ] Identify which tasks are "agent-able" vs require human judgment
- [ ] List the artifacts you create for every project (briefs, designs, code, etc.)
- [ ] Catalog the decisions you make repeatedly

**Start here because:** You can't orchestrate what you haven't defined. Multi-agent systems fail when the task decomposition is wrong.

---

## Part 2: Types of Research Needed

### Research Stream 1: Process & Workflow Analysis (Do First)

**Objective:** Understand your current process deeply enough to decompose it

**Activities:**
- Map your workflow from lead → delivered site
- Identify decision points, dependencies, and handoffs
- Categorize tasks by: creative, technical, administrative, client-facing
- Document your quality checks and how you know something is "done"

**Output:** Process map with task types, dependencies, and time allocation

**Why this matters:** Multi-agent orchestration is fundamentally about task decomposition. Bad decomposition = agents stepping on each other or producing inconsistent work.

---

### Research Stream 2: Multi-Agent Architecture Patterns

**Objective:** Understand how to coordinate multiple AI agents effectively

**Key Questions:**
- Sequential vs parallel agent execution?
- How do agents share context and artifacts?
- How do you handle conflicts between agent outputs?
- What's the human-in-the-loop model?

**Existing Patterns to Research:**

| Pattern | Description | Best For |
|---------|-------------|----------|
| **Pipeline** | Agent A → Agent B → Agent C | Linear workflows with clear handoffs |
| **Supervisor** | One agent delegates to specialist agents | Complex tasks needing coordination |
| **Collaborative** | Agents work on shared artifacts | Iterative refinement |
| **Swarm** | Agents claim tasks from a queue | High parallelism, independent tasks |

**Tools/Frameworks to Evaluate:**
- LangGraph (agent orchestration)
- CrewAI (multi-agent framework)
- AutoGen (Microsoft's multi-agent)
- Custom orchestration with Claude + tool use
- Temporal/Inngest for workflow orchestration

**Output:** Architecture decision record comparing approaches

---

### Research Stream 3: Website-Specific Automation

**Objective:** Identify what can be automated in website creation

**Research Areas:**

**Design Automation:**
- AI design tools (v0, Galileo AI, Uizard)
- Design system generation
- Brand asset creation
- Responsive layout generation

**Development Automation:**
- Code generation quality by framework
- Component library selection
- CMS integration patterns
- Deployment automation

**Content Automation:**
- Copy generation for small businesses
- SEO optimization
- Image selection/generation
- Localization

**Output:** Capability matrix of what's automatable today vs needs human input

---

### Research Stream 4: Competitive & Market Analysis

**Objective:** Understand the landscape and find positioning

**Direct Competitors (AI Website Builders):**
- Wix ADI, Squarespace AI, Framer AI
- v0.dev, Bolt.new, Lovable
- Durable, 10Web AI, Hostinger AI

**Adjacent Solutions:**
- Traditional agencies with AI augmentation
- Freelancer networks
- White-label website services

**Key Questions:**
- What do these tools do well? Where do they fail?
- Why would a small business choose you over Wix ADI?
- What's the price sensitivity in this market?

**Output:** Competitive positioning matrix and differentiation strategy

---

### Research Stream 5: Technology Stack Evaluation

**Objective:** Choose the right technical foundation

**Considerations for AI-Built Sites:**
- Framework with excellent AI code generation (Next.js, Astro)
- Headless CMS for content management
- Component libraries with good defaults
- Deployment platforms with simple DX

**Considerations for Your Platform:**
- How will you store project state?
- How will agents communicate?
- Where does human review happen?
- How do you version/rollback agent work?

**Output:** Technology stack recommendation with rationale

---

## Part 3: Pilot Approach

### Pilot Philosophy

Don't build a platform and hope it works. Instead:

1. **Simulate the platform manually** - Act as the orchestrator yourself
2. **Identify what's hard** - Find the failure modes
3. **Automate incrementally** - Replace yourself piece by piece

### Recommended Pilot Structure

#### Phase 1: Manual Orchestration (2-3 projects)

**Setup:**
- Define 3-5 specialist "agent roles" (e.g., Discovery Agent, Design Agent, Dev Agent)
- Create explicit prompts/instructions for each role
- Use separate Claude sessions for each "agent"
- You act as the orchestrator, passing artifacts between them

**Track:**
- Time spent on each agent's work
- Quality of handoffs (what context was missing?)
- Where you had to intervene
- What artifacts needed to be created

**Goal:** Validate that the decomposition works before automating it

---

#### Phase 2: Semi-Automated Orchestration (3-5 projects)

**Setup:**
- Build simple tooling to manage agent sessions
- Create artifact storage (could just be a folder structure)
- Add structured handoff templates
- Implement basic quality checks

**Track:**
- Reduction in your coordination time
- Agent output quality without intervention
- Client satisfaction
- Total project time

**Goal:** Prove the orchestration model before building full platform

---

#### Phase 3: Evaluate Platform Necessity

After Phase 2, you'll know:
- Whether multi-agent actually saves time
- What the hard coordination problems are
- Whether this is a personal productivity gain or a scalable product

**Decision Point:** Build platform, license/sell the process, or keep as competitive advantage?

---

## Part 4: How to Start Building

### Don't Start With The Platform

The platform is the last thing you build. Start with:

#### Level 1: Process Documentation (Week 1)
- Document your current workflow in detail
- Create templates for each project phase
- Define "done" criteria for each stage

#### Level 2: Agent Prompts & Instructions (Week 2-3)
- Write detailed prompts for each specialist agent
- Create example inputs/outputs
- Define handoff protocols

#### Level 3: Simple Orchestration Scripts (Week 4-6)
- Scripts to initialize projects
- Artifact storage and retrieval
- Basic workflow state management

#### Level 4: Quality & Review Tools (Week 7-8)
- Automated checks for agent outputs
- Human review interfaces
- Feedback capture

#### Level 5: Platform (Only If Validated)
- User management
- Project management UI
- Full workflow automation
- Billing/monetization

### Technology Recommendations

**For the websites you're building:**
```
Framework: Next.js 14+ (App Router)
Styling: Tailwind CSS
Components: shadcn/ui
CMS: Sanity or Payload
Hosting: Vercel
```

**For your orchestration tooling:**
```
Language: TypeScript
Orchestration: Start with scripts, graduate to Temporal
Agent Framework: Direct Claude API with tool use
State: SQLite → PostgreSQL as you scale
File Storage: Local → S3
```

**For eventual platform:**
```
Framework: Next.js (you know it from building sites)
Database: PostgreSQL + Prisma
Auth: Clerk or Auth.js
Queue: Inngest or Trigger.dev
```

---

## Part 5: Recommended Projects

### Project Sequence

| Order | Project | Purpose | Complexity |
|-------|---------|---------|------------|
| 1 | Workflow Documentation | Foundation for everything | Low |
| 2 | Agent Prompt Library | Codify your expertise | Low |
| 3 | Project Scaffolder | Automate project setup | Low |
| 4 | Artifact Manager | Store and retrieve agent outputs | Medium |
| 5 | Manual Orchestration Pilot | Validate multi-agent approach | Medium |
| 6 | Semi-Automated Pilot | Test lightweight automation | Medium |
| 7 | Quality Check Suite | Automated output validation | Medium |
| 8 | Orchestration MVP | Minimal viable orchestration | High |
| 9 | Platform (if validated) | Full product | High |

---

### Project 1: Workflow Documentation

**Deliverable:** Detailed process document covering:
- Client intake and discovery
- Design phase
- Development phase
- Content creation
- Review and revision
- Deployment and handoff

**Format:** Markdown with decision trees and checklists

---

### Project 2: Agent Prompt Library

**Deliverable:** Collection of prompts for specialist agents:
- **Discovery Agent:** Extracts requirements from client info
- **Design Agent:** Creates design system and layouts
- **Content Agent:** Writes copy, CTAs, meta descriptions
- **Dev Agent:** Implements designs in code
- **QA Agent:** Reviews and identifies issues
- **SEO Agent:** Optimizes for search

**Format:** Prompt files with examples and expected outputs

---

### Project 3: Project Scaffolder

**Deliverable:** CLI tool or script that:
- Creates project folder structure
- Initializes git repo with your template
- Sets up configuration files
- Creates agent workspace folders
- Generates initial brief template

**Tech:** Node.js CLI with prompts

---

### Project 4: Artifact Manager

**Deliverable:** Simple system to:
- Store outputs from each agent
- Track versions
- Enable retrieval by other agents
- Log what was produced when

**Tech:** File-based initially, database later

---

### Project 5: Quality Check Suite

**Deliverable:** Automated checks for:
- Accessibility (axe-core)
- Performance (Lighthouse)
- SEO basics
- Responsive behavior
- Link validation
- Content completeness

**Tech:** Playwright + testing utilities

---

## Risk Assessment

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| Multi-agent coordination harder than expected | High | High | Start with manual orchestration pilot |
| Agent outputs inconsistent quality | Medium | High | Strong prompts + quality checks |
| Building platform nobody wants | Medium | High | Validate with pilot before platform |
| Scope creep into full product too early | High | Medium | Strict project sequence |
| Technology choices don't scale | Low | Medium | Start simple, plan migration paths |

---

## Immediate Next Steps

1. **This week:** Document your current workflow in detail
2. **Next week:** Identify your 3 biggest time sinks
3. **Week 3:** Define your agent roles based on workflow decomposition
4. **Week 4:** Write initial prompts for 2-3 core agents
5. **Week 5-6:** Run manual orchestration pilot on real project

---

## Open Questions

- How many sites are you building per month currently?
- What's your current tech stack for client sites?
- Do you have existing templates or start from scratch each time?
- What's your client communication/review process?
- Are you considering this as a solo scaling tool or a team/product?

---

## References

- [LangGraph Documentation](https://langchain-ai.github.io/langgraph/)
- [CrewAI Framework](https://www.crewai.com/)
- [Temporal Workflow Engine](https://temporal.io/)
- [v0.dev](https://v0.dev) - AI UI generation
- [Inngest](https://www.inngest.com/) - Event-driven workflows

---

## Summary

**Where to start:** Document your current process before building anything.

**Research needed:** Process analysis first, then architecture patterns, then competitive landscape.

**Pilot approach:** Manual orchestration → semi-automated → decide on platform.

**Building sequence:** Process docs → agent prompts → simple tools → orchestration → platform (maybe).

**First project:** Workflow documentation. Everything else depends on it.

The key insight: You're not building a platform. You're codifying expertise. The platform is just the container—if you even need one.
