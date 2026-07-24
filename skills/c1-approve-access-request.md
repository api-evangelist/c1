---
name: Approve or deny a C1 access-request task
description: Find a pending ConductorOne access-request task and approve, deny, reassign, or comment on it.
api: openapi/c1-openapi-original.yml
operations:
  - c1.api.task.v1.TaskSearchService.Search
  - c1.api.task.v1.TaskService.Get
  - c1.api.task.v1.TaskActionsService.Approve
  - c1.api.task.v1.TaskActionsService.Deny
  - c1.api.task.v1.TaskActionsService.Comment
  - c1.api.task.v1.TaskActionsService.Reassign
---

# Approve or deny a C1 access-request task

Use this skill to triage ConductorOne (C1) access-request tasks as an agent.

## Auth
- OAuth2 client-credentials. Send `client_id` + `client_secret` in the request
  **body** to `POST https://{tenantDomain}.conductor.one/auth/v1/token`, then use
  the returned bearer token. See `authentication/c1-authentication.yml`.

## Steps
1. **Find pending tasks** — `POST /api/v1/search/tasks`
   (`c1.api.task.v1.TaskSearchService.Search`). Filter to open/pending tasks.
   Paginate with `page_size` + `page_token`; stop when `nextPageToken` is empty
   (see `conventions/c1-conventions.yml`).
2. **Inspect one task** — `GET /api/v1/tasks/{id}`
   (`c1.api.task.v1.TaskService.Get`) to read the requested entitlement, subject
   user, and current step.
3. **Act:**
   - Approve — `POST /api/v1/tasks/{task_id}/action/approve`
     (`c1.api.task.v1.TaskActionsService.Approve`).
   - Deny — `POST /api/v1/tasks/{task_id}/action/deny`
     (`c1.api.task.v1.TaskActionsService.Deny`).
   - Add context without deciding — `POST /api/v1/tasks/{task_id}/action/comment`
     (`c1.api.task.v1.TaskActionsService.Comment`).
   - Hand off — `POST /api/v1/tasks/{task_id}/action/reassign`
     (`c1.api.task.v1.TaskActionsService.Reassign`).

## Notes
- Approvals grant real access — treat as a high-consequence action; confirm the
  subject and entitlement before calling approve.
- There is no idempotency key; do not blindly retry an approve/deny on error —
  re-`Get` the task to check its state first.
