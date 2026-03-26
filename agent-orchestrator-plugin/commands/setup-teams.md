# /setup-teams — Enable Agent Teams for This Project

Configure Claude Code's experimental Agent Teams feature for the current project.

## Usage

```
/setup-teams
```

## What it does

1. **Checks prerequisites**: Verifies Claude Code version (needs v2.1.32+) and tmux availability
2. **Enables Agent Teams**: Adds `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` to project settings
3. **Creates team configuration**: Sets up recommended defaults for agent team workflows
4. **Verifies setup**: Confirms the feature is active

## Steps performed

### 1. Enable the feature flag

Adds to `.claude/settings.json`:
```json
{
  "env": {
    "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"
  }
}
```

### 2. Install tmux (if needed)

Agent Teams work best with split-pane mode so you can see each teammate working.
On Ubuntu/Debian:
```bash
sudo apt install tmux
```

On macOS:
```bash
brew install tmux
```

### 3. Recommended permission pre-approvals

Agent Teams generate lots of permission prompts. Pre-approve common operations:
- File read/write in the project directory
- Running tests
- Git operations

### 4. Usage tips

After setup, start a tmux session before launching Claude Code:
```bash
tmux new -s dev
claude
```

Then describe your team:
```
Create an agent team to build [feature]:
- Teammate 1 (Backend): Design and implement the API endpoints
- Teammate 2 (Frontend): Build the React components
- Teammate 3 (Tests): Write integration tests as the others build
```

## Agent Teams vs Subagents (quick reference)

| | Subagents | Agent Teams |
|---|---|---|
| Communication | Report back to main only | Teammates message each other |
| Context | Shared parent context | Independent 1M token windows |
| Best for | Focused tasks, quick lookups | Complex multi-domain work |
| Cost | Lower (~1x per agent) | Higher (~5x per teammate) |
| Coordination | Main agent relays everything | Self-coordinating via mailbox |

## When to use which

- **Simple delegation** → `/orchestrate` (uses subagents, cheaper, sequential/parallel)
- **Complex collaboration** → `/setup-teams` then use Agent Teams (teammates communicate directly)

Use `/orchestrate` by default. Reach for Agent Teams when agents genuinely need to debate, share mid-task discoveries, or coordinate on shared files.
