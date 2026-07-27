---
name: Onboard a GitLab project to Gitar and verify its status
description: Connect a GitLab project to a Gitar organization, confirm the webhook was configured, then read the project's merge-request blocking status.
api: openapi/gitar-openapi-original.json
operations: [onboardGitlabProject, getGitlabMrStatus, getInstallationHealth]
---

# Onboard a GitLab project to Gitar

Use the Gitar External API (`https://api.gitar.ai/v1`) to connect a GitLab project and
verify Gitar is active on its merge requests. API access is an Enterprise-plan capability.

## Auth
- Create an API token in `app.gitar.ai` → org settings → API → **Create API Token**
  (set an alias and expiration; the token is shown once — copy it immediately).
- Send it on every request: `Authorization: Bearer <token>`.
- A `403 Insufficient scopes` means the token lacks the required scopes.

## Steps
1. **Confirm the code-hosting installation is healthy** — call `getInstallationHealth`
   (`GET /installation/health`). Check `code_hosting.gitlab.status` is `ok`. A `404`
   means no code-hosting installation is connected yet.
2. **Onboard the project** — call `onboardGitlabProject`
   (`POST /external/gitlab/projects`) with either `project_id` or `project_path` in the
   body. Providing neither returns `400`. On success inspect the response `status`,
   `success`, and `webhook_configured` fields; a `404` means the GitLab integration is
   not connected for this organization.
3. **Read merge-request blocking status** — call `getGitlabMrStatus`
   (`GET /external/gitlab/mr_status`) for the project. The `blocked` field reports
   whether Gitar is currently blocking the MR. A `404` means the project is not
   connected to this org.

## Conventions
- All responses are JSON; errors use `{ error_code, message }` (see `errors/gitar-problem-types.yml`).
- Requests are rate limited per organization — back off on `429 Too Many Requests`.
- See `conventions/gitar-conventions.yml` for the full request/response contract.
