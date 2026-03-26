# /follow-plan — Execute a Previously Written Plan

Load a saved implementation plan and execute it through the orchestrator without re-doing the planning phase.

## Usage

```
/follow-plan [path-to-plan]
```

## Examples

```
/follow-plan docs/plans/oauth-feature.md
/follow-plan plans/api-refactor.md
/follow-plan STATUS.md    # if the plan is embedded in the status file
```

If no path is provided, the orchestrator will look for plans in common locations:
1. `docs/plans/*.md`
2. `.claude/plans/*.md`
3. Prompt you to specify a path

## What it does

1. **Reads the plan file** at the specified path
2. **Scans installed agents** (`~/.claude/agents/` and `.claude/agents/`) to verify all agents referenced in the plan are available
3. **Reports any missing agents** — if the plan references an agent that isn't installed, it stops and tells you what's needed
4. **Presents a summary** of the phases it's about to execute
5. **Asks for confirmation** before starting
6. **Asks permission for parallel phases** — if the plan includes any parallel execution, you'll be asked to approve it explicitly
7. **Executes** phase by phase, passing outputs between agents
8. **Synthesizes** results and updates STATUS.md

## Plan file format

The orchestrator expects a plan with clear phases and agent assignments. The standard format from `/orchestrate` works directly:

```markdown
## Implementation Plan: [Feature Name]

### Phase 1: [Phase Name] — [agent-filename]
- Task: [description]
- Output: [deliverable]
- Dependencies: none

### Phase 2: [Phase Name] — [agent-filename]
- Task: [description]
- Output: [deliverable]
- Mode: parallel (pending approval)
- Dependencies: Phase 1

### Phase 3: ...
```

But the orchestrator is flexible — if you wrote the plan in a different format (bullet lists, numbered steps, prose with agent names), it will interpret it reasonably. The key requirements are:
- Clear separation of phases/steps
- Agent names that match installed agent filenames
- Some indication of what each phase should produce

## Workflow

This command is designed for two scenarios:

### 1. Re-running a plan from a previous session
You used `/orchestrate` last session, it saved the plan, and now you want to pick up where you left off or re-execute it.

### 2. Running a hand-written plan
You drafted a plan yourself (or the Software Architect drafted it and you saved it without executing). Now you want the orchestrator to execute it.

## Tips

- **Save plans from `/orchestrate`**: When the orchestrator drafts a plan, it offers to save it. Say yes — this builds your plan library.
- **Edit saved plans**: Plans are just Markdown files. Adjust phases, swap agents, or remove steps before re-running.
- **Partial execution**: If you only want to run some phases, edit the plan file to remove the phases you want to skip, or tell the orchestrator "start from Phase 3" when it asks for confirmation.
