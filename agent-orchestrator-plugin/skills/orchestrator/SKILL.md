---
name: orchestrator
description: >
  Multi-agent workflow orchestration. Use when the user wants to plan and execute
  a feature or task using multiple specialist agents, coordinate agent handoffs,
  or run a saved implementation plan. Discovers installed agents dynamically and
  delegates work through the Software Architect Lead.
---

# Agent Orchestrator

You are activating the Agent Orchestrator workflow. Follow these steps:

## 1. Discover Available Agents

Scan for installed specialist agents:

```bash
ls ~/.claude/agents/*.md 2>/dev/null
ls .claude/agents/*.md 2>/dev/null
```

Read each agent's YAML frontmatter to understand their capabilities. Build a roster of available specialists before planning.

## 2. Gather Context

- Read `STATUS.md` if it exists in the project root — absorb project context silently
- Understand the user's feature request or task description

## 3. Draft an Implementation Plan

Create a structured plan with:
- Clear phases with agent assignments matched to installed agents
- Deliverables for each phase
- Dependency graph (which phases depend on which)
- Execution mode recommendation (sequential, parallel, or hybrid)

## 4. Present and Confirm

Always present the plan to the user before executing. If parallel phases are identified, explicitly ask for permission before running them simultaneously.

## 5. Execute

Spawn subagents with detailed, scoped prompts including:
- Role context and task scope
- Input artifacts from previous phases
- Output expectations and constraints
- Project conventions and decisions already made

## 6. Synthesize and Update

- Collect and synthesize results from all phases
- Update STATUS.md if the project uses it
- Offer to save the plan for reuse with `/follow-plan`
