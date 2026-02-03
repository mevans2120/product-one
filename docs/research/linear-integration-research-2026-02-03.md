# Linear Integration Research for Product One

**Date:** 2026-02-03
**Status:** Complete
**Focus:** Evaluating Linear as the project management layer for AI-agent-driven website builds

---

## Executive Summary

Linear is a strong fit for product-one's workflow. It provides project templates with milestones and pre-configured issues that map directly to your existing skill-based phases (design-system-init, sanity-setup, cms-verify, etc.). The official MCP server enables Claude Code to read, create, and update Linear issues without leaving the terminal. The GitHub integration auto-transitions issue states based on PR activity. The free tier supports unlimited members with up to 250 active issues and 2 teams, which is sufficient to pilot with 2-3 concurrent client projects before committing to a paid plan.

The recommended approach: start on the free tier, create a project template that mirrors your workflow phases, connect the MCP server to Claude Code, and enable the GitHub integration. This gives you issue tracking, workflow visibility, and AI-agent integration with zero custom code.

---

## Table of Contents

1. [Linear API and CLI Capabilities](#1-linear-api-and-cli-capabilities)
2. [Linear + GitHub Integration](#2-linear--github-integration)
3. [Linear + Claude Code Integration](#3-linear--claude-code-integration)
4. [Project Templates in Linear](#4-project-templates-in-linear)
5. [Linear Pricing Analysis](#5-linear-pricing-analysis)
6. [Alternatives Comparison](#6-alternatives-comparison)
7. [Workflow Mapping](#7-workflow-mapping)
8. [Practical Integration Approach](#8-practical-integration-approach)
9. [Risks and Limitations](#9-risks-and-limitations)
10. [Recommendation](#10-recommendation)
11. [Sources](#11-sources)

---

## 1. Linear API and CLI Capabilities

### GraphQL API

Linear exposes its full data model through a public GraphQL API at `https://api.linear.app/graphql`. This is the same API Linear uses internally. Key characteristics:

- **Full CRUD support** for all entities: Issues, Projects, Cycles, Milestones, Comments, Labels, Users, Teams
- **Real-time propagation**: Any mutation via the API is observed in real-time by all connected clients
- **Rate limits**: 1,500 requests per hour per user (API key auth)
- **Authentication**: Personal API keys (scoped to specific permissions and teams) or OAuth 2.1 with refresh tokens

### Official TypeScript SDK

```bash
npm i @linear/sdk
```

The `@linear/sdk` package (currently v70.0.0, MIT license) provides strongly typed models and operations generated from the GraphQL schema. Usage:

```typescript
import { LinearClient } from '@linear/sdk'
const client = new LinearClient({ apiKey: process.env.LINEAR_API_KEY })

// Create an issue
const issue = await client.createIssue({
  teamId: 'team-id',
  title: 'Set up design tokens',
  description: 'Run design-system-init skill',
  projectId: 'project-id',
})

// Update issue state
await issue.update({ stateId: 'done-state-id' })
```

This SDK could be used to build custom automation scripts -- for example, a script that creates a full project with all issues from a template definition stored in your repo.

### Webhooks

Webhooks deliver HTTP POST notifications when data changes. Supported resource types:

- Issues, Comments, Issue attachments
- Projects, Project updates
- Cycles, Labels, Users
- Documents, Emoji reactions, Issue SLAs

Webhook delivery includes retry logic (1 min, 1 hour, 6 hours) and requires HTTPS endpoints. Webhooks can be created through the UI or programmatically via GraphQL mutations.

**Relevant use case for product-one:** A webhook could notify a Slack channel or trigger a script when an issue moves to "Done" -- useful for tracking which workflow phases are complete across client projects.

### CLI Tools

There is no official full-featured CLI from Linear, but several community options exist:

**`@linear/cli` (official, minimal)**
- Two commands: `lin new` (create issue) and `lin checkout` (switch to issue branch)
- Minimal but officially supported

**`schpet/linear-cli` (recommended for Claude Code)**
- Installable via `brew install schpet/tap/linear` or Deno
- Git-aware: detects current issue from branch name
- Key commands: `linear issue list`, `linear issue start`, `linear issue view`, `linear issue pr`
- Explicitly designed for AI agent workflows
- Can be installed as a Claude Code skill via `npx skills add schpet/linear-cli`
- Creates PRs with pre-populated titles and descriptions from Linear issue data

**`rolaca11/linear-cli` (feature-rich)**
- Full issue, project, cycle, milestone, and document management
- Installable for specific agents: `linear skill install -a claude-code -a cursor`
- Commands like `linear issues create --title "Fix bug" --team TECH --priority 2`

**Linearis (by Carlo Zottmann)**
- JSON output optimized for LLM consumption
- Smart ID resolution and structured data output

### What Can Be Automated

| Capability | Method | Complexity |
|-----------|--------|------------|
| Create issues from workflow template | SDK or API | Low |
| Move issues through states | SDK, API, or GitHub integration | Low |
| Create projects with milestones | SDK or API | Low |
| Assign issues to team members | SDK or API | Low |
| Link PRs to issues | GitHub integration (automatic) | None |
| Notify on state changes | Webhooks | Medium |
| Bulk create issues from config file | Custom script with SDK | Medium |
| Sync with external systems | Webhooks + API | Medium-High |

---

## 2. Linear + GitHub Integration

### How It Works

Linear's GitHub integration connects issues to pull requests and commits. The integration is bidirectional and automatic once configured.

### Setup

1. A Linear workspace admin enables the GitHub integration in Settings
2. Select which GitHub repos to connect
3. Each team configures PR automation rules independently

### PR Linking

Issues link to PRs in three ways:

1. **Branch name**: Creating a branch named `eng-123-feature-name` automatically links to issue ENG-123
2. **PR description**: Writing `Closes ENG-123` or `Fixes ENG-123` in the PR body
3. **Commit messages**: Including the issue ID in commit messages

### Automatic State Transitions

The integration can move issues through workflow states based on PR lifecycle:

| PR Event | Default Behavior | Configurable? |
|----------|------------------|---------------|
| Branch created / PR drafted | Move to "In Progress" | Yes |
| PR opened | Move to custom state (e.g., "In Review") | Yes |
| Review requested | Move to "In Review" (if configured) | Yes |
| PR merged | Move to "Done" | Yes |
| PR reverted | Reopen the issue | Automatic |

Teams can customize which workflow state maps to each PR event. For example, you could create an "In Review" state and have PR open trigger that state, while merge triggers "Done."

### Multiple PRs Per Issue

If an issue has multiple open PRs, Linear waits for all of them to be merged before triggering the merge automation. This is relevant for product-one since a single workflow step (like "sanity-setup") might involve multiple PRs.

### GitHub Issues Sync

For repos that receive external GitHub issues (e.g., open source), Linear can sync newly created GitHub issues into Linear teams. Comments, status, assignee, and other states sync bidirectionally. This is less relevant for product-one's use case but good to know.

### Personal Git Automations

Individual developers can enable personal automations in account preferences:

- Auto-assign issues when you create a branch for them
- Auto-move issues to "In Progress" when you start work

### Relevance to Product One

This integration is high-value because it means Claude Code's normal git workflow (create branch, make commits, open PR) automatically updates Linear without any additional code. The workflow becomes:

1. Claude Code reads the next issue from Linear (via MCP or CLI)
2. Creates a branch with the issue ID in the name
3. Does the work (runs the relevant skill)
4. Opens a PR referencing the issue
5. Linear automatically moves the issue through states

No manual status updates needed.

---

## 3. Linear + Claude Code Integration

### Official MCP Server

Linear provides a first-party MCP (Model Context Protocol) server. This is the recommended integration path for Claude Code.

**Endpoint:** `https://mcp.linear.app/mcp` (Streamable HTTP) or `https://mcp.linear.app/sse` (SSE)

**Configuration for Claude Code** (add to `.claude/settings.json` or project config):

```json
{
  "mcpServers": {
    "linear": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://mcp.linear.app/mcp"]
    }
  }
}
```

After adding this config, run `/mcp` in Claude Code to authenticate via OAuth.

**Alternative authentication:** Pass an API key directly via `Authorization: Bearer <token>` header instead of the interactive OAuth flow.

### Available MCP Tools

The MCP server provides tools for:

- **Searching** issues, projects, and comments
- **Creating** new issues, projects, and comments
- **Updating** existing issues, projects, and comments
- More capabilities are actively being added

### What This Enables

With the MCP server connected, you can give Claude Code natural language instructions like:

- "Create a bug in the maggie-mistal project called 'Fix mobile nav overflow' with priority urgent"
- "What issues are still open in the Foundation phase?"
- "Move issue MM-15 to Done and add a comment that the design tokens are configured"
- "Create a new project called 'client-name' using the website build template"

### Claude Code Skill Integration

The `schpet/linear-cli` tool can be installed as a Claude Code skill:

```bash
npx skills add schpet/linear-cli
```

This gives Claude Code structured commands for:
- Listing unstarted issues assigned to the current user
- Starting work on an issue (creates branch)
- Viewing the current issue based on git branch
- Creating PRs with pre-populated data from Linear

### Composio Alternative

Composio offers another MCP integration path that provides broader tool access. However, the official Linear MCP server is simpler and maintained by Linear directly.

### Linear for Agents

Linear has added explicit support for AI agents:

- Agent session threads support resolution and subscriptions
- Agents can be mentioned in issue descriptions (not just comments)
- This is evolving quickly as Linear invests in AI-first workflows

### Practical Implications

The MCP server means Claude Code can serve as the primary interface for Linear during active development. The developer (you) can use the Linear web UI for high-level project oversight, while Claude Code handles the granular issue management during builds.

---

## 4. Project Templates in Linear

### What Templates Include

Project templates can contain:

- **Project metadata**: Name, description, status, lead, members
- **Associated initiatives**: Link to higher-level strategic goals
- **Milestones**: Ordered stages within the project (maps directly to your workflow phases)
- **Pre-configured issues**: Individual tasks within each milestone, including sub-issues
- **Team assignment**: Which team owns the project

### Creating Templates

Three methods:

1. **Settings > Templates** (workspace or team level)
2. **CMD+K** and search "create new project template"
3. **Keyboard shortcut**: `G` then `S` for workspace settings

Workspace-level templates are available to all teams. Team-level templates are restricted to that team.

### Template Scope

- Workspace templates: All teams can use them
- Team templates: Only the owning team can use them
- Teams can set a default template that pre-populates when creating new projects

### Sub-Issues in Templates

When adding an issue to a template, you can add sub-issues via the `...` menu. Issue templates with existing sub-issues can also be incorporated into project templates.

### Using Templates

When creating a new project, the template selector appears in the top-left of the create modal. You can also use CMD+K to search for "create project from template."

### Mapping to Product One Workflow

A single project template could encode your entire website build workflow:

```
Project Template: "Website Build - Standard"
├── Milestone: Setup
│   ├── Issue: Project scaffolding (Next.js + TypeScript + Tailwind)
│   ├── Issue: GitHub repo setup
│   ├── Issue: Vercel project creation
│   └── Issue: Environment variables configuration
├── Milestone: Foundation
│   ├── Issue: Run site-audit skill (if redesign)
│   ├── Issue: Run design-system-init skill
│   ├── Issue: Configure design tokens
│   ├── Issue: Set up typography and fonts
│   └── Issue: Create base layout components
├── Milestone: CMS & Content
│   ├── Issue: Run sanity-setup skill
│   ├── Issue: Define content schemas
│   ├── Issue: Configure presentation mode
│   ├── Issue: Set up draft mode API routes
│   ├── Issue: Content migration (if redesign)
│   └── Issue: Run cms-verify skill
├── Milestone: Services & Features
│   ├── Issue: Build service/offering pages
│   ├── Issue: Contact form implementation
│   ├── Issue: Feature module integration (booking/portfolio/blog)
│   └── Issue: Mobile responsive verification
├── Milestone: Polish
│   ├── Issue: Run performance-audit skill
│   ├── Issue: Image optimization
│   ├── Issue: Font loading optimization
│   ├── Issue: Accessibility review
│   └── Issue: SEO meta tags and structured data
└── Milestone: QA & Launch
    ├── Issue: Cross-browser testing
    ├── Issue: Client review and feedback
    ├── Issue: Final content review
    ├── Issue: DNS and domain configuration
    └── Issue: Production deployment
```

### Limitations

- Templates cannot include conditional logic (e.g., "only include site-audit if redesign")
- You may want 2-3 template variants: "New Build," "Redesign," "Simple Landing Page"
- Templates do not support cycle assignment -- issues are added to cycles separately

---

## 5. Linear Pricing Analysis

### Tier Comparison

| Feature | Free ($0) | Basic ($10/user/mo) | Business ($16/user/mo) | Enterprise (Custom) |
|---------|-----------|---------------------|------------------------|---------------------|
| Members | Unlimited | Unlimited | Unlimited | Unlimited |
| Teams | 2 | 5 | Unlimited | Unlimited |
| Active Issues | 250 | Unlimited | Unlimited | Unlimited |
| Integrations (GitHub, Slack) | All | All | All | All |
| API & Webhooks | Yes | Yes | Yes | Yes |
| MCP Server | Yes | Yes | Yes | Yes |
| AI Agents | Yes | Yes | Yes | Yes |
| Admin Roles | No | Yes | Yes | Yes |
| File Uploads | Limited | Unlimited | Unlimited | Unlimited |
| Private Teams | No | No | Yes | Yes |
| Guest Access | No | No | Yes | Yes |
| Triage Intelligence | No | No | Yes | Yes |
| SLAs | No | No | Yes | Yes |
| SAML/SSO | No | No | No | Yes |

*Prices are annual billing. Monthly billing is typically higher.*

### Recommendation for Product One

**Start with the Free tier.** Here is the reasoning:

- **250 active issues** is sufficient for 2-3 concurrent client projects (each project has roughly 20-30 issues, and completed issues can be archived)
- **2 teams** is enough (one "Client Projects" team is likely sufficient; you could add a second for "Internal/Platform" work)
- **All integrations included** -- GitHub, Slack, API, webhooks, and MCP server are all available on free
- **Unlimited members** -- if you bring on collaborators or want client visibility, no per-seat cost
- **AI agent support** -- the MCP server works on the free tier

**When to upgrade to Basic ($10/mo):** If you consistently exceed 250 active issues (managing 10+ concurrent projects) or need more than 2 teams (e.g., separating client work from platform development from content).

**When to consider Business ($16/mo):** If you want guest access for clients to view their project progress, or if you need private teams to separate client work.

### Cost Comparison

| Scenario | Linear Cost | Notes |
|----------|-------------|-------|
| Solo operator, 2-3 projects | $0/mo | Free tier covers this entirely |
| Solo + 1 contractor, 5 projects | $0/mo | Still within free limits |
| Small team (3 people), 10+ projects | $30/mo | Basic tier, 3 seats |
| Team with client access | $48/mo | Business tier, 3 seats |

---

## 6. Alternatives Comparison

### Comparison Matrix

| Capability | Linear | GitHub Issues/Projects | Notion | Jira |
|-----------|--------|----------------------|--------|------|
| **Speed/UX** | Fastest (3.7x Jira) | Fast | Moderate | Slow |
| **Developer focus** | Purpose-built | Native to code | General-purpose | Enterprise-focused |
| **Project templates** | Yes (with milestones) | Limited (issue templates) | Yes (database templates) | Yes |
| **AI/MCP integration** | Official MCP server | Copilot (different) | Notion AI | None native |
| **Claude Code integration** | MCP + CLI skills | Native (gh CLI) | No | No |
| **GitHub PR linking** | Automatic state sync | Native | Manual | Bidirectional |
| **Cycles/Sprints** | Built-in | No native sprints | Manual | Built-in |
| **Free tier** | 250 issues, unlimited users | Unlimited (public), limited (private) | Unlimited pages | 10 users |
| **API quality** | Excellent GraphQL | Good REST + GraphQL | Good REST | Complex REST |
| **Keyboard-driven** | Yes (core design) | Partial | No | No |
| **Client visibility** | Guest access (Business) | Collaborator access | Share pages | Viewer roles |
| **Overhead** | Low | Lowest | Medium | High |
| **Roadmap/Timeline** | Built-in | Project boards | Manual Gantt | Built-in |

### GitHub Issues/Projects

**Pros:**
- Zero context switching -- lives where the code is
- Free for public repos, included in GitHub plans
- GitHub Actions can automate project board updates
- Native to your existing workflow

**Cons:**
- Project views are less polished than Linear
- No native cycles/sprints concept
- Limited template support for project-level workflows
- No MCP server for Claude Code (though `gh` CLI works)
- Harder to get a high-level view across multiple repos/projects

**Verdict:** Good if you want minimal tooling overhead, but lacks the structured project management that Linear provides. GitHub Projects is improving but still feels like an afterthought compared to Linear's purpose-built experience.

### Notion

**Pros:**
- Flexible -- can model any workflow
- Great for documentation alongside tasks
- Client-friendly interface
- Good templates ecosystem

**Cons:**
- Not developer-focused -- no native git integration
- Slow compared to Linear for issue management
- Requires significant manual setup to replicate Linear's workflow
- No automatic PR/issue linking
- MCP integration exists but is less mature for dev workflows

**Verdict:** Better for documentation and client-facing project pages. Could complement Linear rather than replace it. Use Notion for client briefs, content documents, and project wikis; use Linear for actual development task tracking.

### Jira

**Pros:**
- Industry standard, enterprise-grade
- Extensive customization
- Large plugin ecosystem

**Cons:**
- Slow, complex UI
- Massive overkill for a small operation
- Expensive for what you get
- Poor developer experience
- No Claude Code integration

**Verdict:** Not recommended for product-one. Designed for large enterprise teams, not solo developers or small agencies using AI agents.

### Recommendation

**Linear as primary task/project management** with these complements:

- **GitHub** for code review, CI/CD, and repository management (already in use)
- **Notion or simple markdown docs** for client-facing documentation and content briefs (optional)
- **Slack** for notifications from Linear (if you want real-time alerts)

---

## 7. Workflow Mapping

### Current Product One Workflow

Based on the existing documentation, the workflow follows this pattern:

```
Redesign: site-audit → design-system-init → [build] → sanity-setup → [content/migrate] → cms-verify → [polish] → performance-audit
New Build: design-system-init → [build] → sanity-setup → [content] → cms-verify → [polish] → performance-audit
```

With phases: Setup, Foundation, Services & Content, Blog & Media, Polish, QA & Launch.

### Mapping to Linear Concepts

| Product One Concept | Linear Concept | Notes |
|--------------------|----------------|-------|
| Client project (e.g., maggie-mistal) | **Project** | One Linear project per client website |
| Workflow phase (e.g., Foundation) | **Milestone** | Milestones within the project |
| Individual task (e.g., "configure design tokens") | **Issue** | Issues assigned to milestones |
| Skill execution (e.g., run design-system-init) | **Issue** (with label "skill") | Tracked as issues, labeled for filtering |
| Sprint/iteration | **Cycle** | Optional: 1-2 week cycles for active builds |
| All client projects | **Team** | One team called "Client Projects" |
| Product-one platform work | **Team** (second) | "Platform" or "Internal" team |
| Strategic goals (e.g., "scale to 10 projects/month") | **Initiative** | Groups related projects |

### Proposed Linear Structure

```
Workspace: Product One
├── Team: Client Projects
│   ├── Project: maggie-mistal
│   │   ├── Milestone: Setup
│   │   ├── Milestone: Foundation
│   │   ├── Milestone: Services & Content
│   │   ├── Milestone: Blog & Media
│   │   ├── Milestone: Polish
│   │   └── Milestone: QA & Launch
│   ├── Project: next-client
│   └── ...
├── Team: Platform (Internal)
│   ├── Project: Skills Development
│   ├── Project: Template System
│   └── Project: Workflow Automation
└── Labels (workspace-level):
    ├── skill:site-audit
    ├── skill:design-system-init
    ├── skill:sanity-setup
    ├── skill:cms-verify
    ├── skill:performance-audit
    ├── type:build
    ├── type:content
    └── type:config
```

### Issue Workflow States

Customize the team workflow to match your development process:

```
Backlog → Todo → In Progress → In Review → Done → Canceled
```

With the GitHub integration, "In Progress" and "In Review" transitions happen automatically when you create branches and open PRs.

### Cycle Usage

Cycles (sprints) are optional but useful if you want time-boxed focus:

- **Option A: No cycles** -- Work through milestones sequentially, no time pressure
- **Option B: 1-week cycles** -- Assign milestone issues to weekly cycles for pacing
- **Option C: Project-as-cycle** -- Each client project gets a 2-4 week cycle representing the full build

Recommendation: Start without cycles (Option A). Add them later if you find you need time-boxing to stay on track across multiple concurrent projects.

### Labels for Skill Tracking

Create labels prefixed with `skill:` to track which issues involve running specific Claude Code skills:

```
skill:site-audit
skill:design-system-init
skill:sanity-setup
skill:cms-verify
skill:performance-audit
```

This allows you to filter and view all skill-related tasks across projects, track how often each skill is used, and identify patterns in skill execution time.

### Views

Create custom views for operational visibility:

- **"Active Builds"** -- All projects in progress, grouped by milestone completion
- **"Skill Queue"** -- All issues with `skill:*` labels, sorted by priority
- **"Blocked"** -- Issues with "blocked" status across all projects
- **"This Week"** -- Issues in the current cycle (if using cycles)

---

## 8. Practical Integration Approach

### Phase 1: Setup (Day 1)

**Step 1: Create Linear workspace**
- Sign up at linear.app
- Create workspace named "Product One" (or your preferred name)

**Step 2: Create teams**
- Team 1: "Client Projects" -- for all client website work
- Team 2: "Platform" -- for product-one internal work (skills, templates, automation)

**Step 3: Configure workflow states**
- Keep the default: Backlog, Todo, In Progress, In Review, Done, Canceled
- Optionally add: "Blocked" and "Client Review" states

**Step 4: Create labels**
```
skill:site-audit
skill:design-system-init
skill:sanity-setup
skill:cms-verify
skill:performance-audit
type:build
type:content
type:config
type:client-feedback
priority:must-have
priority:nice-to-have
```

**Step 5: Enable GitHub integration**
- Settings > Integrations > GitHub
- Connect your GitHub account
- Select repos to link (e.g., maggie-mistal, and future client repos)
- Configure PR automation per team:
  - PR opened → "In Review"
  - PR merged → "Done"
  - PR reverted → Reopen issue

**Step 6: Create API key**
- Settings > Account > Security & Access
- Create API key with read/write permissions
- Save securely (you will need this for CLI and MCP)

### Phase 2: Template Creation (Day 1-2)

**Step 7: Create project templates**

Create 2-3 project templates in Settings > Templates:

**Template: "Website Build - Redesign"**
```
Milestones and issues:
├── Setup
│   ├── Create GitHub repo from template
│   ├── Set up Vercel project
│   ├── Configure environment variables
│   └── Initial deployment verification
├── Foundation
│   ├── [skill] Run site-audit on existing site
│   ├── [skill] Run design-system-init
│   ├── Create base layout (header, footer, nav)
│   ├── Configure responsive breakpoints
│   └── Set up error and loading states
├── CMS & Content
│   ├── [skill] Run sanity-setup
│   ├── Define content schemas
│   ├── Content migration from old site
│   ├── [skill] Run cms-verify
│   └── Client content review
├── Services & Features
│   ├── Build service/offering pages
│   ├── Contact form with validation
│   ├── Feature module (booking/portfolio/blog)
│   └── Mobile responsive QA
├── Polish
│   ├── [skill] Run performance-audit
│   ├── Implement audit recommendations
│   ├── Accessibility review (WCAG 2.1 AA)
│   ├── SEO optimization (meta, OG, structured data)
│   └── Cross-browser testing
└── QA & Launch
    ├── Client final review
    ├── Content completeness check
    ├── DNS and domain setup
    ├── Production deployment
    └── Post-launch monitoring (48h)
```

**Template: "Website Build - New"**
Same as above but without site-audit and content migration issues.

**Template: "Simple Landing Page"**
A stripped-down version for simpler projects.

### Phase 3: Claude Code Integration (Day 2)

**Step 8: Configure MCP server**

Add to your Claude Code MCP configuration (project-level or global):

```json
{
  "mcpServers": {
    "linear": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://mcp.linear.app/mcp"]
    }
  }
}
```

Run `/mcp` in Claude Code to authenticate.

**Step 9: Install Linear CLI skill (optional, complementary to MCP)**

```bash
brew install schpet/tap/linear
linear auth login
linear config
```

Or install as a Claude Code skill:
```bash
npx skills add schpet/linear-cli
```

**Step 10: Test the integration**

In Claude Code, try:
- "List all open issues in Client Projects"
- "Create a new project called 'test-project' in Client Projects"
- "Create an issue titled 'Test issue' in test-project"

### Phase 4: First Real Project (Day 3+)

**Step 11: Create project from template**
- In Linear: Create new project > Select "Website Build - Redesign" template
- Name it after the client (e.g., "maggie-mistal")
- Assign yourself as project lead

**Step 12: Connect the GitHub repo**
- Ensure the client's GitHub repo is connected via the GitHub integration
- Verify branch naming convention works (e.g., `mm-123-design-tokens`)

**Step 13: Work through issues with Claude Code**

The workflow for each issue becomes:

```
1. In Claude Code: "What's the next issue to work on in maggie-mistal?"
   → Claude reads from Linear via MCP

2. Claude Code creates a branch: mm-123-configure-design-tokens
   → Linear auto-moves issue to "In Progress"

3. Claude Code runs the relevant skill (e.g., design-system-init)
   → Work happens in the repo

4. Claude Code opens a PR: "Closes MM-123"
   → Linear auto-moves issue to "In Review"

5. You review and merge the PR
   → Linear auto-moves issue to "Done"

6. Repeat for next issue
```

### Phase 5: Iterate and Optimize (Ongoing)

- Review which template issues are consistently skipped or added -- update templates
- Track total project time by milestone to identify bottlenecks
- Add custom views for operational visibility
- Consider cycles if managing 3+ concurrent projects
- Evaluate whether to upgrade to Basic tier based on issue count

---

## 9. Risks and Limitations

### Known Limitations

| Limitation | Impact | Mitigation |
|-----------|--------|------------|
| 250 active issue cap (free tier) | Limits concurrent projects | Archive completed issues promptly; upgrade to Basic if needed |
| 2 team cap (free tier) | May need more separation | Use labels and projects within a single team |
| No conditional templates | Cannot have "if redesign, include site-audit" | Create separate templates for each project type |
| Templates cannot assign to cycles | Issues must be manually added to cycles | Skip cycles initially; add when needed |
| MCP server still evolving | Some actions may not be available yet | Fall back to CLI or web UI for unsupported actions |
| Rate limit (1,500 req/hr) | Could be hit during heavy automation | Unlikely for your scale; monitor if scripting bulk operations |
| Not client-facing by default | Clients cannot view progress without Business tier | Use a separate client-facing tool or share milestone summaries manually |

### Risks

**Low Risk:**
- Linear is well-funded, fast-growing (150,000+ teams), and actively developing. Low risk of abandonment.
- The free tier is generous enough to fully evaluate before committing money.

**Medium Risk:**
- MCP server capabilities are still expanding. You may hit gaps where a desired action is not yet supported, requiring fallback to the API or web UI.
- If your operation grows significantly, the per-user pricing ($10-16/user/month) can add up faster than GitHub Issues (free) or Notion.

**Considerations:**
- Linear is less suited for client-facing project management. If clients want to see their project board, you will need the Business tier for guest access, or you will need to provide updates through another channel.
- Linear's opinionated design means you cannot customize the UI or workflow as extensively as Notion or Jira. This is actually a feature for most developer teams but could be limiting if you have unusual workflow requirements.

---

## 10. Recommendation

### Verdict: Adopt Linear for Product One

Linear is the right tool for this workflow. Here is the reasoning:

1. **Template-driven project creation** maps directly to your skill-based workflow phases
2. **MCP server for Claude Code** means the AI agent can manage its own task queue
3. **GitHub integration** eliminates manual status updates during development
4. **Free tier** covers your current scale with room to grow
5. **Speed and keyboard-driven UX** aligns with a developer-centric, terminal-first workflow
6. **CLI tools designed for AI agents** (schpet/linear-cli) show the ecosystem is moving in the direction you need

### Implementation Priority

| Priority | Action | Effort | Impact |
|----------|--------|--------|--------|
| 1 | Create workspace, teams, labels | 30 min | Foundation |
| 2 | Build project templates (2-3 variants) | 1-2 hours | High -- standardizes every new project |
| 3 | Enable GitHub integration | 15 min | High -- automatic state transitions |
| 4 | Configure MCP server for Claude Code | 15 min | High -- AI agent integration |
| 5 | Run first real project through Linear | 1 project | Validation -- proves the workflow |
| 6 | Install linear-cli as Claude Code skill | 15 min | Medium -- enhanced agent workflow |
| 7 | Create custom views | 30 min | Medium -- operational visibility |
| 8 | Evaluate cycles for multi-project management | After 2-3 projects | Low initially |

### Total Setup Time: ~3-4 hours

This gets you from zero to a fully integrated project management system where Claude Code can read its task queue from Linear, work through issues using your skills, and automatically update progress through the GitHub integration.

### What Success Looks Like

After setup, creating a new client project should take 2 minutes:

1. Create project from template in Linear (1 min)
2. Connect the GitHub repo (1 min)
3. Start working -- Claude Code pulls issues from Linear, works through them sequentially, and progress updates automatically

You gain:
- **Visibility**: See all projects and their phase completion at a glance
- **Consistency**: Every project follows the same workflow template
- **Automation**: No manual status updates during development
- **History**: Full audit trail of what was done, when, and in what order
- **Scalability**: Adding a new project is template-driven, not setup-from-scratch

---

## 11. Sources

### Official Linear Documentation
- [Linear API and Webhooks](https://linear.app/docs/api-and-webhooks)
- [Linear MCP Server Documentation](https://linear.app/docs/mcp)
- [Linear Project Templates](https://linear.app/docs/project-templates)
- [Linear GitHub Integration](https://linear.app/docs/github-integration)
- [Linear Project Milestones](https://linear.app/docs/project-milestones)
- [Linear Pricing](https://linear.app/pricing)
- [Linear Billing and Plans](https://linear.app/docs/billing-and-plans)
- [Linear Conceptual Model](https://linear.app/docs/conceptual-model)
- [Linear Developer SDK](https://linear.app/developers/sdk)
- [Linear Webhooks (Developer Docs)](https://linear.app/developers/webhooks)
- [Linear Personal Automations](https://linear.app/docs/personal-automations)
- [Linear Method (Principles & Practices)](https://linear.app/method/introduction)

### Linear Integrations
- [Linear Claude Integration](https://linear.app/integrations/claude)
- [Linear AI Clients Integrations](https://linear.app/integrations/ai-clients)
- [Linear GitHub Integration Page](https://linear.app/integrations/github)
- [Linear Automations Integrations](https://linear.app/integrations/automations)

### CLI Tools
- [schpet/linear-cli (GitHub)](https://github.com/schpet/linear-cli) -- Agent-friendly Linear CLI
- [rolaca11/linear-cli (GitHub)](https://github.com/rolaca11/linear-cli) -- Feature-rich Linear CLI
- [@linear/sdk on npm](https://www.npmjs.com/package/@linear/sdk) -- Official TypeScript SDK
- [Linear GitHub Repository](https://github.com/linear/linear) -- Official tools and SDK source
- [Linearis CLI](https://zottmann.org/2025/09/03/linearis-my-linear-cli-built.html) -- JSON-output CLI for LLM agents

### MCP and AI Integration
- [Linear MCP Server Changelog](https://linear.app/changelog/2025-05-01-mcp)
- [Composio Linear MCP Integration](https://composio.dev/blog/how-to-set-up-linear-mcp-in-claude-code-to-automate-issue-tracking)
- [Linear + Claude Code Guide (Cyrus)](https://www.atcyrus.com/stories/linear-claude-code-integration-guide)

### Comparison and Reviews
- [Linear Review 2026 (work-management.org)](https://work-management.org/software-development/linear-review/)
- [Linear Alternatives (monday.com)](https://monday.com/blog/rnd/linear-alternatives/)
- [Notion vs Linear (Everhour)](https://everhour.com/blog/notion-vs-linear/)
- [Linear Alternatives for GitHub-First Teams (Zenhub)](https://www.zenhub.com/blog-posts/the-best-linear-alternatives-for-github-first-teams)
- [How to Use Linear Guide (Morgen)](https://www.morgen.so/blog-posts/linear-project-management)
- [How We Use Linear (Plum)](https://build.plumhq.com/how-we-use-linear/)

### Pricing
- [Linear Pricing Breakdown (Bardeen)](https://www.bardeen.ai/answers/how-much-does-linear-app-cost)
- [Linear Pricing 2026 (G2)](https://www.g2.com/products/linear/pricing)

### Automation Platforms
- [Zapier Linear Automations](https://zapier.com/blog/automate-linear/)
- [GitHub + Linear via Zapier](https://zapier.com/apps/github/integrations/linear)
- [n8n Linear Integration](https://n8n.io/integrations/webhook/and/linear/)
- [Pipedream Linear Integration](https://pipedream.com/apps/linear-app/integrations/http)
