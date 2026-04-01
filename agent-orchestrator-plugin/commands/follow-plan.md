# /follow-plan

Use the Task tool to spawn the `software-architect-lead` subagent now. Pass the following as the complete task prompt:

---
The user has invoked /follow-plan with the following input: $ARGUMENTS

First, determine what the user provided:

## **If $ARGUMENTS looks like a file path** (starts with `.`, `/`, or ends in `.md`)

1. Read the plan file at that path
2. Scan installed agents at ~/.claude/agents/ and .claude/agents/ — verify every agent referenced in the plan is available
3. If any required agents are missing, stop and tell the user exactly which agents are needed
4. Present a summary of the phases you are about to execute, **ask if the plan should be executed on a git worktree**, then ask for confirmation
5. If the plan includes parallel phases, **ask for explicit permission** before running them simultaneously
6. Execute phase by phase using the Task tool to spawn each agent, passing outputs between phases
7. Synthesize all results and present a final summary; **make sure you give the user the name of the git worktree, if one was used**.

## **If $ARGUMENTS is empty or no path was provided**, check these locations in order

1. docs/plans/*.md
2. .claude/plans/*.md
If plans are found, list them and ask the user which to run. If none are found, ask the user to *rerun* the command and provide a file path or describe what they want, give a *brief* example.

### After the user chooses a file, follow these steps

1. Read the plan that the file path points to.
2. Scan installed agents at ~/.claude/agents/ and .claude/agents/ - verify every agent referenced in the plan is available (filepath provided)
3. If any required agents are missing, stop and tell the user exactly which agents are needed
4. Present a summary of the phases you are about to execute, **ask if the plan should be executed on a seperate git worktree**, then ask for confirmation
5. If the plan includes parallel phases, **ask for explicit permission** before running them simultaneously
6. Execute phase by phase using the Task tool to spawn each agent, passing outputs between phases
7. Synthesize all results and present a final summary; **make sure you give the user the name of the git worktree, if one was used**.

## **If $ARGUMENTS is a natural language description** (not a path)

Treat it as a feature request and skip the planning phase — go straight to execution:

1. Discover available agents by scanning ~/.claude/agents/ and .claude/agents/ — read each agent's YAML frontmatter
2. Read STATUS.md if it exists
3. Draft a lean implementation plan based on the description, matched to installed agents
4. Present the plan, **ask if the plan should be executed on a seperate git worktree**, then ask for confirmation before executing
5. If any phases are independent and could run in parallel, **ask for explicit permission first**
6. Execute phase by phase using the Task tool to spawn each specialist agent with scoped prompts
7. Pass each agent's output as input to the next phase
8. Synthesize all results and present a final summary; **make sure you give the user the name of the git worktree, if one was used**.
9. Offer to save the plan to docs/plans/ for future reuse

---

Wait for the subagent to complete and relay its final output to the user.
