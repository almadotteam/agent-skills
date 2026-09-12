# Alma Agent Skills

Skills that teach coding agents (Claude Code, Cursor, Codex, OpenCode, and
others) how to build on [Alma](https://alma.team) — connecting to its API and
MCP server, setting up a workspace, and debugging topics.

## Install

```bash
npx skills add almadotteam/agent-skills
```

The CLI lists the skills in this repo, lets you pick which ones and which
agents to install them to, and records the choice in your `skills-lock.json`.
Run `npx skills update` to pull new versions.

## Skills

| Skill | What it covers |
| --- | --- |
| `alma-api` | Alma's public REST API at `api.alma.team/v1` — topics, sources, workflows and executions, running tools, pending actions |
| `alma-mcp` | Connecting an MCP client to `mcp.alma.team` and using the `alma_*` tools |
| `alma-org-interview` | Interviewing a user about their organization, then building Alma's org map and topic schema from the answers |
| `alma-setup` | Setting up a new Alma workspace end to end — profile, source connections, starter workflows, invitations |
| `alma-topic-debugging` | Diagnosing why a topic is missing, stale, or not receiving updates, and fixing it |

Each skill is a folder under `skills/` with a `SKILL.md`; your agent reads the
frontmatter up front and loads the body when a task matches.

## About this repo

This repository is published automatically from Alma's development repository —
it is a read-only mirror, so changes arrive through syncs rather than pull
requests here.
