---
name: alma-org-interview
description:
  Interview a user about how their organization works, then build Alma's org map
  and design the topic schema from the answers. Use when a user wants Alma to
  learn their organization, asks to set up their team structure or knowledge
  schema, or when starting a fresh Alma workspace that has no topics yet.
---

# Organization interview

The goal is two artifacts: a populated org map (people, roles, reporting lines)
and a topic schema (the entity types Alma tracks). Both come out of one
structured conversation. Requires an MCP connection to Alma (see the alma-mcp
skill).

## The interview

Work through these in order, one question at a time — let answers breed
follow-ups before moving on:

1. **What does the company do, and for whom?** Anchors everything else.
2. **Who is on the team?** Names, roles, who reports to whom. Small orgs can be
   enumerated; for larger ones, capture the leadership layer and the teams under
   each.
3. **What entities does the work revolve around?** Customers, deals, candidates,
   projects, vendors, properties — whatever the team would keep a dossier on.
   For each: what facts matter about one, and which facts change often.
4. **Where does information about those entities live today?** Email, Slack, a
   CRM, spreadsheets. This tells you which sources Alma should watch.
5. **What should Alma keep up to date without being asked?** This separates
   schema types worth tracking from nice-to-haves.

## Building the org map

Use the suggest tools as answers come in — every one lands as
`status='suggested'` for the user to review in Alma, so propose freely and tell
the user to accept or dismiss the batch afterward:

- `alma_list_people` / `alma_list_groups` — always check before proposing; never
  suggest a duplicate.
- `alma_create_person` — one per teammate mentioned, with reporting relationship
  when known.
- `alma_create_group` — one per role that came up.
- `alma_assign_group` — connect people to roles.

## Designing the topic schema

Topic types are created in the Alma console (Console → Schema), not through a
tool. Your deliverable is a schema spec the user applies there: for each entity
type from question 3, a name, a one-line description, and the fields a dossier
should carry — each field with a type and a note on where the value comes from.

Example shape (fictional):

```
Type: Client
Fields:
  - status (select: prospect / active / churned) — from CRM stage
  - main contact (person) — from email threads
  - renewal date (date) — from the contract folder
```

Keep the first schema small — three or four types the team touches weekly beat a
taxonomy of twelve. Types can grow fields later.

## Seeding topics

Once the user has created the types, propose starter topics with
`alma_propose_topic` — the type id is required, and always pass `facts` from the
interview so an accepted topic opens with content. Duplicate names are refused
with the existing topic returned: `active`/`paused` means update that one
instead, `suggested`/`draft` is your own earlier proposal still in review,
`archived` was dismissed — respect it.

## Wrap up

End by summarizing what is now waiting for review: suggested people, roles,
assignments, and topics. The user accepts them inside Alma — nothing goes live
without that.
