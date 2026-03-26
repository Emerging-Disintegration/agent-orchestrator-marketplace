# Orchestration Patterns — Multi-Agent Workflow Reference

## Overview

This document covers the three orchestration approaches available in Claude Code and when to use each one.

## Approach 1: Subagent Orchestration (via `/orchestrate`)

**What it is**: Your main Claude Code session spawns focused subagents using the `Task` tool. Each subagent gets a scoped prompt, does its work, and returns results to the main session.

**How agents communicate**: They don't — the main session acts as the relay. Agent A's output is passed as input to Agent B by the orchestrator.

**Cost**: Low. Subagents use their own context but are typically on Sonnet. Results are summarized back.

**Best for**:
- Most development tasks
- Clear sequential pipelines (A → B → C)
- Tasks where agents don't need to debate or share mid-task discoveries
- Budget-conscious workflows

**Example**:
```
/orchestrate Build a REST API for user management with JWT auth
```

The orchestrator plans:
```
Phase 1: Software Architect — design API endpoints and data model
Phase 2: Backend Developer — implement endpoints (depends on Phase 1)
Phase 3: API Tester — write tests (depends on Phase 2)
Phase 4: Security Auditor — review auth implementation (depends on Phase 2)
  → Phases 3 & 4 run in parallel since they're independent
Phase 5: Technical Writer — document the API (depends on all above)
```

### Execution Patterns

#### Sequential
```
A → B → C → D
```
Each agent waits for the previous one. Use when each phase depends on the prior output.

#### Parallel (requires your permission)
```
    ┌→ B ─┐
A → ┤     ├→ D
    └→ C ─┘
```
Independent agents run simultaneously. The orchestrator will **always ask you before parallelizing** — you'll see which phases are candidates and can approve or request sequential instead. Parallel uses more tokens since each agent has its own context.

#### Hybrid
```
A → [parallel: B, C] → D → [parallel: E, F]
```
Plan first, parallelize implementation, then integrate. Most common pattern for features.

---

## Approach 2: Agent Teams (via `/setup-teams`)

**What it is**: Multiple Claude Code instances running as a coordinated team. Each teammate has its own full context window (1M tokens) and can message other teammates directly via a mailbox system.

**How agents communicate**: Peer-to-peer messaging. Any teammate can send findings to any other teammate without going through the lead.

**Cost**: High. Each teammate is a separate Claude instance (~5x cost of single session).

**Best for**:
- Complex problems requiring debate/exploration
- When Agent A's mid-task discovery changes Agent B's approach
- Code review with multiple perspectives
- Debugging with competing hypotheses
- Cross-layer work where frontend/backend/tests need to stay in sync

**Example**:
```
Create an agent team to redesign the options pricing module:
- Teammate 1 (Architect): Evaluate current implementation and propose new async architecture
- Teammate 2 (Quant): Validate pricing model accuracy and suggest improvements to Greeks calculations
- Teammate 3 (Critic): Challenge both proposals, find edge cases, test with extreme market conditions
```

---

## Approach 3: Manual Multi-Session (DIY)

**What it is**: You run multiple Claude Code sessions yourself (using git worktrees, tmux panes, or separate terminals) and manually coordinate.

**How agents communicate**: Through you. You copy relevant context between sessions.

**Cost**: Moderate. You control exactly what runs.

**Best for**:
- When you want full control over every decision
- Simple parallel tasks (run tests in one session while coding in another)
- Learning how multi-agent workflows feel before automating them

---

## Decision Flowchart

```
Is this a simple, focused task?
  YES → Single session, no orchestration needed
  NO ↓

Do agents need to communicate mid-task?
  YES → Agent Teams (/setup-teams)
  NO ↓

Are there 2+ independent phases?
  YES → /orchestrate (subagent pipeline with parallel phases)
  NO → /orchestrate (sequential pipeline)
```

## Routing Rules for the Orchestrator

When the `software-architect-lead` agent is planning, it uses these heuristics:

### Parallel dispatch (ALL conditions must be met):
- 3+ unrelated tasks or independent domains
- No shared state between tasks
- Clear file boundaries with no overlap

### Sequential dispatch (ANY condition triggers):
- Tasks have dependencies (B needs output from A)
- Shared files or state (merge conflict risk)
- Unclear scope (need to understand before proceeding)

### Escalate to Agent Teams (ANY condition triggers):
- Agents need to debate or challenge each other's work
- Mid-task discoveries would change other agents' approaches
- The task involves adversarial review (security audit + implementation happening simultaneously)
- Cross-cutting concerns where tight coordination matters

---

## Cost Comparison

| Approach | Typical Cost (relative) | Coordination | Communication |
|---|---|---|---|
| Single session | 1x | None | N/A |
| Subagent pipeline | 2-4x | Orchestrator manages | Via orchestrator only |
| Agent Teams | 5-15x | Self-coordinating | Peer-to-peer mailbox |
| Manual multi-session | Varies | You manage | Through you |

---

## Tips

1. **Start with `/orchestrate`**. It's cheaper and handles 80% of multi-agent needs.
2. **Save plans for reuse**. The orchestrator offers to save plans — say yes. Use `/follow-plan` to re-execute them.
3. **Graduate to Agent Teams** when you notice the orchestrator relay is a bottleneck.
4. **Always read STATUS.md first** — the orchestrator does this automatically.
5. **Pre-approve permissions** when using Agent Teams to avoid constant prompts.
6. **Use `CLAUDE_CODE_SUBAGENT_MODEL=claude-sonnet-4-5-20250929`** to save tokens on subagent work while keeping the orchestrator on Opus.
7. **Parallel is opt-in**. The orchestrator will always ask before running phases in parallel. Default to sequential if you're unsure — it's cheaper and easier to debug.

---

## Reusable Plans with `/follow-plan`

One of the most powerful patterns is building a library of reusable plans:

```
docs/plans/
├── new-api-endpoint.md      # Standard backend feature pipeline
├── ui-component.md          # Design → implement → test pipeline
├── bug-investigation.md     # Reality Checker → domain agent → QA
├── release-prep.md          # Writer → reviewer → DevOps
└── full-stack-feature.md    # Architecture → parallel FE/BE → integration
```

### Creating a plan library

1. Use `/orchestrate` for a task → orchestrator drafts a plan → approve and save it
2. Generalize the plan: replace specific feature names with `[FEATURE]` placeholders
3. Next time you need a similar workflow: `/follow-plan docs/plans/ui-component.md`

### Editing plans before execution

Plans are just Markdown. Before running `/follow-plan`, you can:
- Remove phases you don't need
- Swap agent assignments
- Add notes or constraints to specific phases
- Change sequential to parallel (the orchestrator will still ask for confirmation)
