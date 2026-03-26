# Claude Code Agent Teams — Reference Guide

## What Are Agent Teams?

Agent Teams let you coordinate multiple Claude Code instances working together on a shared project. One session acts as the **team lead**, coordinating work and assigning tasks. **Teammates** work independently, each with their own 1M-token context window, and can communicate directly with each other via a mailbox system.

This is different from subagents: subagents report back to a single parent and can't talk to each other. Agent Team teammates can share findings, challenge each other's assumptions, and coordinate autonomously.

## Status

- **Experimental** — research preview, disabled by default
- **Requires**: Claude Code v2.1.32+ and Claude Opus 4.6
- **Requires**: Pro ($20/mo) or Max ($100-200/mo) plan

## Setup

### 1. Enable the feature flag

Add to your `.claude/settings.json`:
```json
{
  "env": {
    "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"
  }
}
```

Or set it in your shell:
```bash
export CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1
```

### 2. Install tmux (recommended for split-pane mode)

```bash
# Ubuntu/Debian
sudo apt install tmux

# macOS
brew install tmux
```

### 3. Start a tmux session before launching Claude

```bash
tmux new -s dev
claude
```

## Usage

Describe your team in natural language. Claude creates the team, spawns teammates, and coordinates:

```
Create an agent team to explore this CLI tool design:
- Teammate 1 (UX): Focus on command structure and user workflows
- Teammate 2 (Architecture): Focus on module design and data flow
- Teammate 3 (Devil's Advocate): Challenge assumptions and find edge cases
```

## Architecture

| Component | Role |
|---|---|
| **Team Lead** | Your main session. Creates team, spawns teammates, assigns tasks, synthesizes results |
| **Teammates** | Independent Claude Code instances with their own context windows |
| **Task List** | Shared work items with dependency tracking; tasks auto-unblock when dependencies complete |
| **Mailbox** | Inbox-based messaging system for direct agent-to-agent communication |

## Terminal Modes

- **In-process (default)**: All teammates share your terminal. Use `Shift+Down` to cycle between them.
- **Split panes (tmux/iTerm2)**: Each teammate gets its own visible pane. Much easier to monitor.

**Note**: Split panes don't work in VS Code's integrated terminal, Windows Terminal, or Ghostty.

## Best Practices

### Team Size
Start with **3-5 teammates**. More than that creates coordination overhead that outweighs benefits. Give each teammate 5-6 focused tasks.

### Pre-approve Permissions
Each teammate generates permission prompts independently. Pre-approve common operations (file read/write, test running, git) to reduce friction.

### Best Use Cases
- **Research and review**: Multiple perspectives investigating different aspects simultaneously
- **New modules or features**: Each teammate owns a separate piece
- **Debugging with competing hypotheses**: Test different theories in parallel
- **Cross-layer coordination**: Frontend, backend, and tests each owned by a different teammate

### When NOT to Use
- Simple, linear tasks (use a single session or subagents instead)
- Tasks where agents need to edit the same files (merge conflict risk)
- Quick lookups or focused research (subagents are cheaper and faster)

## Troubleshooting

| Problem | Solution |
|---|---|
| Teammates not appearing | Press `Shift+Down` to cycle. Verify tmux is installed for split panes. |
| Too many permission prompts | Pre-approve common operations in settings |
| Lead finishes too early | Tell it to wait for teammates to complete before proceeding |
| Stale tmux sessions | `tmux ls` to list, `tmux kill-session -t [name]` to clean up |
| Teammates stop on errors | Check their output in split pane, then tell lead to instruct retry |

## Cost Awareness

Each teammate is a separate Claude instance. Token costs scale linearly — a 3-teammate session costs roughly 3x a single session. Reserve Agent Teams for work that genuinely benefits from parallel collaboration. For simpler delegation, use the `/orchestrate` command with subagents.

## Further Reading

- Official docs: https://code.claude.com/docs/en/agent-teams
- Plugin system: https://code.claude.com/docs/en/plugins
