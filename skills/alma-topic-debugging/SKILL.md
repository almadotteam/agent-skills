---
name: alma-topic-debugging
description:
  Diagnose why an Alma topic is missing, stale, or not receiving topic updates,
  and fix it. Use when a user reports that Alma isn't learning something, a
  topic hasn't updated, updates are wrong or duplicated, or a topic they expect
  to see doesn't appear.
---

# Topic troubleshooting

A topic update travels a pipeline: a connected source produces content, Alma
extracts candidate updates, candidates wait for review, accepted updates land on
the dossier. "The topic isn't updating" means one stage is broken — walk them in
order instead of guessing.

Inspect with `alma_get_topic` and `alma_get_topic_memory` over MCP, or
`GET /v1/topics/{id}/summary` and `/v1/topics/{id}/updates` with an API key.

## Stage 1 — does the topic exist, and in what state?

`alma_list_topics` (or ask the user to check Memory in the app). Status is the
first diagnosis:

- `suggested` / `draft` — proposed but never accepted; it collects nothing until
  someone approves it in Memory. Fix: review and accept.
- `archived` — someone dismissed it. Don't recreate it silently; ask.
- `paused` — live dossier, updates suspended.
- Not visible at all — check folder access rules: topics live in folders with
  allowlist permissions, and a topic the user can't see is indistinguishable
  from one that doesn't exist. Same for API keys bound to read subjects or a
  topic allowlist: an empty read is not proof of absence.

## Stage 2 — is the source feeding it?

Most "no updates" cases die here.

- **Connection health.** `GET /v1/connections` or the Connections page. Revoked
  OAuth (someone uninstalled the Slack app, a Google token expired) fails
  quietly — the source just stops producing.
- **Binding.** A source being connected is not the same as being in scope for
  this topic's workflow. Check which sources the responsible workflow reads; an
  unbound source is invisible to it.
- **Volume.** If the source produced nothing relevant since the last update, the
  pipeline is healthy and the topic is simply current. Confirm before fixing
  anything.

## Stage 3 — is the workflow running?

`GET /v1/workflows/{id}/executions` shows the run history;
`GET /v1/executions/{id}` a single run. No recent runs means a trigger or
schedule problem, not a topic problem. A run that stopped mid-way often shows
why in its steps.

## Stage 4 — is something waiting on a human?

`GET /v1/pending-actions` (or `alma_list_asks`). Extraction that proposed
updates which nobody reviewed looks exactly like extraction that found nothing.
A pending action blocking the pipeline is fixed by a decision, not a retry —
surface it to the user.

## Wrong or duplicated content

- Duplicate topics: creation refuses a name the organization already carries and
  returns the existing topic — merge intent onto the existing dossier rather
  than working around the refusal with a variant name.
- Wrong facts on a dossier: propose a correction with
  `alma_propose_topic_update` rather than editing history; the review step is
  the safety net.

## Report format

Tell the user which stage broke, the evidence, and the single fix — not the
whole checklist. "The Slack connection was revoked on <date>; reconnect it under
Connections and the topic resumes" beats a tour of the pipeline.
