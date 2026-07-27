---
name: Check Gitar installation health
description: Query Gitar's installation health across connected code-hosting and integration platforms (GitHub, GitLab, Jira, Slack) to confirm the integration is operational.
api: openapi/gitar-openapi-original.json
operations: [getInstallationHealth]
---

# Check Gitar installation health

Confirm Gitar is correctly connected to your code hosts and integrations before
relying on its reviews.

## Auth
- `Authorization: Bearer <token>` (token created in `app.gitar.ai` org settings → API).

## Steps
1. Call `getInstallationHealth` (`GET /installation/health`).
2. Read `code_hosting` in the response. Each platform block reports a `status`:
   - `code_hosting.github.status`
   - `code_hosting.gitlab.status` (plus `host`, `group`)
   - `code_hosting.jira.status` (plus `host`, `failure_reason`)
   - `code_hosting.slack.status` (plus `team_id`, `failure_reason`)
3. Treat any non-`ok` status (and its `failure_reason`) as an integration that needs
   reconnecting. A `404` means no code-hosting installation is connected.

## Conventions
- JSON responses; error envelope `{ error_code, message }`.
- `401` = missing/invalid token, `403` = insufficient scopes, `429` = rate limited.
- See `conventions/gitar-conventions.yml`.
