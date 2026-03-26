# Agent Orchestrator Plugin

A Claude Code plugin that automates multi-agent workflows. Instead of manually copying prompts between agents, describe a feature and let the Software Architect Lead plan, delegate, and coordinate everything.

## What This Solves

**Before**: You type a prompt to the Software Architect → read the plan → manually copy each agent prompt → paste it → switch agents → repeat for every step.

**After**: `/orchestrate Add OAuth2 auth` → the orchestrator discovers your installed agents → drafts a plan → you approve → agents run automatically in sequence (parallel only with your permission) → results synthesized.

## Installation

### From the marketplace (recommended)

```bash
# 1. Add the marketplace
/plugin marketplace add Emerging-Disintegration/agent-orchestrator-marketplace

# 2. Install the plugin
/plugin install agent-orchestrator@agent-orchestrator
```

### Local development

```bash
claude --plugin-dir /path/to/agent-orchestrator-plugin
```

## Prerequisites

- **Agents installed** in `~/.claude/agents/` — from agency-agents, custom agents, or any source. The orchestrator discovers them dynamically.
- **Claude Code v2.1.32+** (for Agent Teams support)
- **tmux** (optional, for Agent Teams split-pane mode)

## Commands

| Command | What it does |
|---|---|
| `/orchestrate [feature]` | Plan and execute a multi-agent workflow via subagents |
| `/follow-plan [path]` | Execute a previously saved plan without re-planning |
| `/setup-teams` | Enable Agent Teams for the current project |
| `/init-docs` | Create `docs/` folder with orchestration reference |

## Quick Start

```bash
# 1. Start Claude Code with the plugin
claude --plugin-dir ./agent-orchestrator-plugin

# 2. Create your reference docs (one-time)
/init-docs

# 3. Orchestrate a feature
/orchestrate Build a dashboard showing real-time options Greeks

# 4. Later, re-run a saved plan
/follow-plan docs/plans/dashboard-feature.md
```

## How It Works

```
You: /orchestrate [feature description]
         │
         ▼
  Software Architect Lead
  ┌─ Scans ~/.claude/agents/ for available specialists
  ├─ Reads STATUS.md for project context
  ├─ Analyzes the feature scope
  ├─ Matches phases to installed agents
  └─ Presents plan for your approval
         │
    [You approve]
         │
    [Parallel phases? → asks your permission first]
         │
         ▼
  Executes pipeline:
  Phase 1: Agent A (sequential)
         │ output
         ▼
  Phase 2: Agent B + Agent C (parallel, you approved)
         │ outputs
         ▼
  Phase 3: Agent D (sequential, depends on B+C)
         │
         ▼
  Results synthesized → STATUS.md updated
  → Offers to save plan for /follow-plan reuse
```

## Key Behaviors

### Dynamic agent discovery
The orchestrator scans `~/.claude/agents/` and `.claude/agents/` at the start of every run. It reads each agent's YAML frontmatter to understand capabilities. No static catalog needed — it works with whatever you have installed.

### Parallel requires permission
The orchestrator will **never** run phases in parallel without asking first. When it identifies independent phases, it'll present them and ask whether you want parallel (faster, more tokens) or sequential (cheaper, easier to follow).

### Plan saving and reuse
After execution, the orchestrator offers to save the plan to `docs/plans/`. Saved plans can be re-executed with `/follow-plan` — no re-planning needed. Edit the Markdown to adjust before re-running.

## Agent Teams vs Subagent Orchestration

The plugin supports both approaches:

- **`/orchestrate`** and **`/follow-plan`** use **subagents** — cheaper, the orchestrator relays between agents. Good for 80% of tasks.
- **`/setup-teams`** enables **Agent Teams** — teammates talk directly to each other. Use for complex collaborative work where agents need to debate or share mid-task discoveries.

See `docs/ORCHESTRATION-PATTERNS.md` for detailed guidance on when to use which.

## Reference Docs

Run `/init-docs` to generate these in your project:

- `docs/AGENT-TEAMS.md` — Agent Teams setup and usage
- `docs/ORCHESTRATION-PATTERNS.md` — Workflow patterns, decision flowchart, and reusable plan guide
- `docs/plans/` — Directory for saved plans (starts empty)

## Compatibility

- Works with any agents in `~/.claude/agents/` or `.claude/agents/` — the orchestrator discovers them dynamically

