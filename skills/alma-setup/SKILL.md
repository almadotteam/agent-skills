---
name: alma-setup
description:
  Set up a new Alma workspace end to end — organization profile, source
  connections, starter workflows from templates, team invitations, org map, and
  topic schema. Use when a user is getting started with Alma, asks how to
  onboard their team, or wants to connect their tools to Alma.
---

# Setting up Alma

Order matters: profile first (it shapes what Alma suggests), then sources
(nothing works without data), then structure. Requires an MCP connection to Alma
(see the alma-mcp skill for connecting).

## 1. Organization profile

`alma_onboarding_update_profile` and `alma_onboarding_update_org` record who the
user is and what the company does. Two sentences of honest description here
improve every downstream suggestion — do this before anything else.

## 2. Connect sources

`alma_onboarding_list_integrations` shows what can be connected and what already
is. The OAuth flows themselves run in the browser under Connections in the Alma
console — send the user there for each source; an agent cannot complete OAuth on
their behalf.

Priority order: the system where work is discussed (Slack or email) first, then
the calendar, then the system of record (CRM or similar). One connected source
that matters beats five that don't.

Some sources distinguish an organization-level connection from personal ones
(Slack is the common case) — if a member's content isn't showing up later, their
personal connection is usually the missing piece.

## 3. Starter workflows

`alma_onboarding_list_templates` lists ready-made workflow templates;
`alma_onboarding_create_from_template` instantiates one. Start with one or two —
a workspace that immediately does something visible is what convinces a team.
Custom workflows can come later via `alma_create_workflow`.

## 4. Team

`alma_onboarding_send_invitations` invites teammates by email. Do this after
sources and a first workflow exist, so invitees land in a workspace that already
shows value.

## 5. Org map and schema

Run the org interview (the alma-org-interview skill): it captures people, roles,
and reporting lines, and produces the topic schema the user applies at Console →
Schema. This is the step that turns Alma from connected to knowledgeable.

## 6. Working agreements

Standing instructions live as directives (`alma_create_directive`) — e.g.
"weekly summaries go out Friday morning" (fictional example). Actions Alma isn't
sure about surface as pending actions for approval; show the user where those
appear so nothing silently waits on them.

## Done looks like

A profile that describes the org, at least one healthy source under Connections,
one workflow that has run (check its executions), the team invited, and a first
accepted topic in Memory. If any of these is missing, the setup isn't finished —
pick up the missing step rather than adding more on top.
