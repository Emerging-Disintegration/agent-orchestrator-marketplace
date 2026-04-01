# /orchestrate

Use the Task tool to spawn the `software-architect-lead` subagent now. Pass the following as the complete task prompt:

---
The user wants to orchestrate this feature: $ARGUMENTS

Follow your complete orchestration protocol:

1. Discover available agents by scanning ~/.claude/agents/ and .claude/agents/ — read each agent's YAML frontmatter
2. Read STATUS.md if it exists in the project root
3. Draft an implementation plan with clear phases and agent assignments matched to installed agents
4. Present the plan to the user, **ask if the plan should be executed on a git worktree**, and wait for approval before executing anything
5. If any phases are independent and could run in parallel, **explicitly ask the user for permission first**
6. Execute phase by phase using the Task tool to **spawn each specialist agent with scoped prompts**
7. Pass each agent's output as input to the next phase
8. Synthesize all results and present a final summary to the user; **make sure you give the user the name of the git worktree, if one was used**
9. Offer to save the plan to docs/plans/ for reuse with /follow-plan

---

Wait for the subagent to complete and relay its final output to the user.
