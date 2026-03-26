# /orchestrate — Multi-Agent Feature Orchestration

Activate the Software Architect Lead to plan and coordinate a multi-agent workflow.

## Usage

```
/orchestrate [feature description]
```

## What it does

1. Activates the `software-architect-lead` agent
2. Reads STATUS.md for project context (if present)
3. Analyzes the feature request
4. Drafts an implementation plan with agent assignments
5. Presents the plan for your approval
6. On approval, delegates to specialist agents in the correct order
7. Synthesizes results and updates STATUS.md

## Examples

```
/orchestrate Add user authentication with OAuth2 and session management
/orchestrate Refactor the options pricing module to use async API calls
/orchestrate Build a dashboard component showing portfolio Greeks in real-time
```

## How agents communicate

- **Sequential tasks**: Each agent receives the previous agent's output as context
- **Parallel tasks**: Independent agents run simultaneously, results are collected
- **Hybrid**: Architecture/planning first, then parallel implementation, then integration

The orchestrator handles all of this — you just describe what you want built.
