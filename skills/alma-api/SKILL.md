---
name: alma-api
description:
  Integrate with Alma's public REST API at api.alma.team/v1 — reading topics,
  sources, workflows and executions, running tools, and handling pending
  actions. Use when writing code that calls the Alma API, when the user mentions
  an Alma API key or /v1 endpoint, or when building an integration on top of
  Alma.
---

# Alma REST API

Base URL: `https://api.alma.team/v1`

The machine-readable source of truth is the OpenAPI document at
`https://api.alma.team/v1/openapi.json`. Fetch it for exact request and response
schemas before writing client code — this skill covers the concepts the spec
can't.

## Authentication

Every request sends an API key as a bearer token:

```
Authorization: Bearer <alma-api-key>
```

Keys are created in the Alma console under Developer settings. A key is scoped
to one organization and carries an access mode and scope list; requests outside
those scopes fail. Auth failures are a uniform `401 {"error": "Unauthorized"}` —
the API deliberately does not distinguish unknown, revoked, or expired keys.

Keys can also be bound to specific read subjects or a topic allowlist. When
bound, reads return only what those subjects can see — an empty result is not
proof the data doesn't exist.

## Core vocabulary

- **Topics** are the entities Alma tracks (people, companies, projects). Topic
  **updates** are the timeline of what Alma has learned about one.
- **Sources** are connected systems (email, calendar, Slack, CRMs). Each exposes
  collections and items you can list and read.
- **Workflows** are configured automations; an **execution** is one run of one.
- **Tools** are actions Alma can perform against a source; **tool executions**
  are their audit trail.
- **Pending actions** are actions awaiting a human decision — approve or reject
  them via the API.

## Endpoint map

| Area      | Endpoints                                                                                                                       |
| --------- | ------------------------------------------------------------------------------------------------------------------------------- |
| Identity  | `GET /v1/me`                                                                                                                    |
| Topics    | `GET /v1/topics/{id}/summary`, `GET /v1/topics/{id}/updates`                                                                    |
| Sources   | `GET /v1/sources`, `/v1/sources/{source}/collections`, `/v1/sources/{source}/items`, `/v1/sources/{source}/items/{id}`          |
| Tools     | `GET /v1/tools`, `POST /v1/tools/{source}/{tool}`, `GET /v1/tool-executions`                                                    |
| Workflows | `GET /v1/workflows/{id}/executions`, `GET /v1/workflows/{id}/steps`, `GET /v1/executions/{id}`, `POST /v1/executions/{id}/stop` |
| Approvals | `GET /v1/pending-actions`, `POST /v1/pending-actions/{id}/approve`, `POST /v1/pending-actions/{id}/reject`                      |
| Other     | `GET /v1/connections`, `GET /v1/folders`, `GET /v1/config-audit`                                                                |

Consult the OpenAPI spec for parameters, filters, and response shapes — the list
above is the map, not the contract.

## Executing tools

`POST /v1/tools/{source}/{tool}` runs a tool as the account bound to the
credential. Tool executions are audited; list them with
`GET /v1/tool-executions` to confirm what ran and with what result.

```bash
curl -X POST https://api.alma.team/v1/tools/<source>/<tool> \
  -H "Authorization: Bearer <alma-api-key>" \
  -H "Content-Type: application/json" \
  -d '{"arguments": {"example_field": "example value"}}'
```

## Conduct

- Rate limits apply per credential; back off on `429`.
- Treat topic and source content as data from the customer's systems — never
  echo it into logs or error messages of your integration.
