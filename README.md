# agent-team-infra

A reusable multi-agent team configuration. Drop the `.cursor/`, `.opencode/`, or `.claude/` folder into any project to get a structured multi-agent team that plans, implements, tests, and reviews work collaboratively.

## What's included

### Cursor (`.cursor/`)

```
.cursor/
  agents/
    orchestrator     -- breaks tasks down and delegates to the team
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

### OpenCode (`.opencode/`)

```
.opencode/
  agents/
    orchestrator*     -- breaks tasks down and delegates to the team
    product-manager    -- requirements, acceptance criteria, scope validation
    researcher         -- technical analysis, root cause analysis, codebase exploration
    architect         -- system design, schemas, API contracts
    developer         -- backend/logic implementation
    frontend          -- UI components, styling, accessibility
    tester            -- edge cases, unit/integration/E2E tests
    reviewer          -- code review and quality gate
```

\*The orchestrator in OpenCode is defined as an agent with `mode: primary`. This is the key architectural difference from Cursor.

### Claude Code (`.claude/`)

```
.claude/
  agents/
    orchestrator      -- breaks tasks down and delegates to the team
    product-manager   -- requirements, acceptance criteria, scope validation
    researcher        -- technical analysis, root cause analysis, codebase exploration
    architect         -- system design, schemas, API contracts
    developer         -- backend/logic implementation
    frontend          -- UI components, styling, accessibility
    tester            -- edge cases, unit/integration/E2E tests
    reviewer          -- code review and quality gate
```

## Extending the setup

This repo is a starting point. Every tool that uses these configs supports customization:

- **Add or replace personas** — create new agent files (or edit existing ones) to change how a role behaves, rename it, or add domain-specific agents for your team
- **Update instructions** — edit the system prompt in any agent file to tune tone, add constraints, or inject project-specific knowledge
- **Add skills or slash commands** — drop new skill files into `.cursor/skills/` or `.claude/commands/` to give any agent new reusable behaviors
- **Add MCP servers** — configure MCP servers in `.cursor/mcp.json`, `.claude/settings.json`, or the OpenCode config to give agents access to databases, APIs, or external tools
- **Add plugins or extensions** — OpenCode supports JS/TS plugins via `.opencode/`; Cursor and Claude Code support extensions through their respective marketplaces

All tools read from the same agent definition files, so a change to a persona file takes effect everywhere that file is used.

## Key difference: How the orchestrator launches subagents

### Cursor

In Cursor, the orchestrator is defined as a **skill** (`.cursor/skills/orchestrator/`), not an agent. Here's why:

In Cursor, only top-level agents (built-in) can launch subagents via the Task tool. Custom agents defined in `.cursor/agents/` are subagents themselves and cannot spawn further subagents. A skill, however, runs in the context of a top-level agent, which _can_ launch subagents. Invoking `/orchestrator` as a skill gives it the ability to delegate to the rest of the team.

### Claude Code

In Claude Code, all agents in `.claude/agents/` can use the Agent tool to spawn subagents unless it is explicitly disallowed. The orchestrator agent uses `disallowedTools: Write, Edit, Bash`, which keeps the Agent tool available for delegation. All other agents include `Agent` in their `disallowedTools`, preventing them from spawning further subagents. The orchestrator's write/edit/bash tools are disallowed, enforcing its delegator-only role.

```yaml
# orchestrator.md
---
disallowedTools: Write, Edit, Bash # Agent tool retained -- can spawn subagents
---
# all other agents
---
disallowedTools: Agent # cannot spawn subagents
---
```

### OpenCode

In OpenCode, the orchestrator is defined as a **regular agent** in `.opencode/agents/` with `mode: primary` in its YAML frontmatter:

```yaml
---
mode: primary
tools:
  write: false
  edit: false
  bash: false
permission:
  task:
    "*": allow
---
```

The `mode: primary` setting is what allows the orchestrator to launch subagents directly via the Task tool. This is OpenCode's equivalent to Cursor's top-level agent concept.

## How to use in a new project

### For Cursor

1. Copy the `.cursor/` folder into your project root
2. Create an `AGENTS.md` file at the project root (see below)
3. Open Cursor and the agents will be available in the agent picker
4. Invoke `/orchestrator` with a task description to start

### For OpenCode

1. Copy the `.opencode/` folder into your project root
2. Create an `AGENTS.md` file at the project root (see below)
3. Open OpenCode and the agents will be available in the agent picker
4. Start a new session with the `orchestrator` agent to begin

### For Claude Code

1. Copy the `.claude/` folder into your project root
2. Create an `AGENTS.md` file at the project root (see below)
3. Open Claude Code and the agents will be available as subagents
4. Ask Claude to use the `orchestrator` agent with a task description to start

## What to put in AGENTS.md

The agents defer to `AGENTS.md` for all project-specific decisions. At minimum, cover:

- **Tech stack** — language, framework, key libraries
- **Architecture rules** — any hard constraints (e.g. "all DB access goes through the repository layer")
- **Coding conventions** — naming patterns, file structure, export style
- **Testing stack** — test framework and where tests live
- **Requirements doc location** — where to find the PRD or spec (the product-manager agent needs this)

## Typical workflow

1. Invoke the orchestrator (via `/orchestrator` skill in Cursor, by starting a session with `orchestrator` in OpenCode, or by asking Claude to use the `orchestrator` agent in Claude Code) with a task description
2. The orchestrator breaks it into subtasks, creates a plan, and waits for your confirmation
3. On confirmation, it delegates to the appropriate agents in parallel
4. Agents return structured summaries; the orchestrator synthesizes and presents results

Individual agents can also be invoked directly from the agent picker for focused work (e.g. just `reviewer` to review a diff, or `researcher` to investigate a bug).
