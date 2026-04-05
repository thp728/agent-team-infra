# agent-team-infra

A reusable Cursor agent team configuration. Drop the `.cursor/` folder into any project to get a structured multi-agent team that plans, implements, tests, and reviews work collaboratively.

## What's included

```
.cursor/
  agents/
    orchestrator*     -- breaks tasks down and delegates to the team
    product-manager   -- requirements, acceptance criteria, scope validation
    researcher        -- technical analysis, root cause analysis, codebase exploration
    architect         -- system design, schemas, API contracts
    developer         -- backend/logic implementation
    frontend          -- UI components, styling, accessibility
    tester            -- edge cases, unit/integration/E2E tests
    reviewer          -- code review and quality gate
  skills/
    orchestrator/     -- slash command to invoke the orchestrator as a skill
```

*The orchestrator is defined as a skill, not an agent. Here's why: in Cursor, only top-level agents (built-in) can launch subagents via the Task tool — custom agents defined in `.cursor/agents/` are subagents themselves and cannot spawn further subagents. A skill, however, runs in the context of a top-level agent, which *can* launch subagents. Invoking `/orchestrator` as a skill gives it the ability to delegate to the rest of the team.

## How to use in a new project

1. Copy the `.cursor/` folder into your project root
2. Create an `AGENTS.md` file at the project root — this is how you tell the agents about your project's conventions (see below)
3. Open Cursor and the agents will be available in the agent picker

## What to put in AGENTS.md

The agents defer to `AGENTS.md` for all project-specific decisions. At minimum, cover:

- **Tech stack** — language, framework, key libraries
- **Architecture rules** — any hard constraints (e.g. "all DB access goes through the repository layer")
- **Coding conventions** — naming patterns, file structure, export style
- **Testing stack** — test framework and where tests live
- **Requirements doc location** — where to find the PRD or spec (the product-manager agent needs this)

## Typical workflow

1. Invoke `/orchestrator` with a task description
2. The orchestrator breaks it into subtasks, creates a plan, and waits for your confirmation
3. On confirmation, it delegates to the appropriate agents in parallel
4. Agents return structured summaries; the orchestrator synthesizes and presents results

Individual agents can also be invoked directly from the agent picker for focused work (e.g. just `reviewer` to review a diff, or `researcher` to investigate a bug).
