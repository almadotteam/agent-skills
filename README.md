# Alma Agent Skills

Distributable skills that teach coding agents (Claude Code, Cursor, Codex,
OpenCode, and others) how to build on Alma. This folder is the source of truth;
CI mirrors it to the public `almadotteam/agent-skills` repository, which is what
users install from. Nothing here depends on the monorepo.

## How distribution works

Skills follow the [Agent Skills](https://agentskills.io) format: one folder per
skill containing a `SKILL.md` with YAML frontmatter (`name`, `description`). The
agent reads only the frontmatter up front and loads the body when the task
matches — so the `description` is the trigger, written for the agent deciding
whether to load it.

The install channel is the [`skills` CLI](https://github.com/vercel-labs/skills)
(an npm package run via `npx`/`bunx`). GitHub is the registry — there is no
separate npm publish for the skills themselves:

```bash
npx skills add almadotteam/agent-skills
```

That command lists the skills in this repo, lets the user pick which ones and
which agents to install them to, and records the choice in the consumer's
`skills-lock.json` (`npx skills update` pulls new versions). Installs surface
the repo on [skills.sh](https://skills.sh) automatically.

A second, optional channel is a Claude Code plugin marketplace
(`.claude-plugin/marketplace.json` + `/plugin install`), which bundles the same
skill folders for Claude Code specifically. Add it only if plugin-managed
updates turn out to matter; `npx skills` covers Claude Code already.

## Layout

```
agent-skills/
  skills/
    alma-api/SKILL.md             # public REST API (api.alma.team/v1)
    alma-mcp/SKILL.md             # Alma MCP server (mcp.alma.team)
    alma-org-interview/SKILL.md   # interview → org map + topic schema
    alma-setup/SKILL.md           # workspace onboarding end to end
    alma-topic-debugging/SKILL.md # why a topic isn't updating, and the fix
```

The CLI discovers anything matching `skills/<name>/SKILL.md`, up to three levels
deep.

## Authoring rules

- Frontmatter `name` is the install identifier: lowercase, hyphens.
- `description` says what the skill covers **and when to use it**, in third
  person — it is the only text the agent sees before deciding to load the body.
- Keep the body under ~500 lines. Split reference material into sibling files
  and link them relatively; agents read those on demand.
- Every example is fictional: placeholder ids, invented names, `<angle-bracket>`
  tokens for anything the reader supplies. No real organization data, ever.
- No secrets. Skills ship to strangers' machines.

## Publishing

The `Publish Agent Skills` workflow
(`.github/workflows/publish-agent-skills.yml`) mirrors this folder to
`almadotteam/agent-skills` on every push to `main` that touches it. The public
repo is a build artifact — never edit it directly; changes land here through
normal PR review.

One-time setup:

1. Create the public `almadotteam/agent-skills` repository with a `main` branch
   (an empty initial commit is enough).
2. Install the release-bot GitHub App on it — the workflow mints its push token
   from the existing `RELEASE_BOT_APP_ID` / `RELEASE_BOT_PRIVATE_KEY` pair,
   scoped to this one repository. The App needs the contents write permission;
   if it only carries read (it was introduced for reading spect releases), grant
   that on the App before the first sync.

Verify discovery locally before merging skill changes:
`npx skills add ./agent-skills --list` from the monorepo root.
