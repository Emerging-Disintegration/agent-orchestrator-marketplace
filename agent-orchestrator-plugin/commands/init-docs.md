# /init-docs — Create Agent Reference Documentation

Populate a `docs/` folder in the project root with reference documentation for Agent Teams and agency-agents.

## Usage

```
/init-docs
```

## What it does

1. Creates `docs/` directory in the project root (if it doesn't exist)
2. Creates `docs/plans/` directory for saving reusable orchestration plans
3. Generates two reference documents:
   - `docs/AGENT-TEAMS.md` — Claude Code Agent Teams documentation
   - `docs/ORCHESTRATION-PATTERNS.md` — Common multi-agent workflow patterns and decision guide
4. These docs serve as quick-reference for the orchestrator and for you

**Note**: The orchestrator discovers installed agents dynamically by scanning `~/.claude/agents/` and `.claude/agents/` — no static agent catalog is needed.

## When to use

Run this once per project that will use multi-agent workflows. The docs folder gives both you and the orchestrator agent quick access to agent capabilities without needing to search the web or remember syntax.
