---
name: Grant or revoke access in C1
description: Create a grant or revoke task for an app entitlement and a user in ConductorOne.
api: openapi/c1-openapi-original.yml
operations:
  - c1.api.app.v1.AppSearch.Search
  - c1.api.app.v1.AppEntitlements.List
  - c1.api.user.v1.UserSearch.Search
  - c1.api.task.v1.TaskService.CreateGrantTask
  - c1.api.task.v1.TaskService.CreateRevokeTask
---

# Grant or revoke access in C1

Use this skill to provision or de-provision access via ConductorOne (C1) tasks.

## Auth
- OAuth2 client-credentials (credentials in body) → bearer token. See
  `authentication/c1-authentication.yml`.

## Steps
1. **Find the app** — `POST /api/v1/search/apps`
   (`c1.api.app.v1.AppSearch.Search`) to resolve the target app id.
2. **Find the entitlement** — `GET /api/v1/apps/{app_id}/entitlements`
   (`c1.api.app.v1.AppEntitlements.List`) to pick the entitlement to grant/revoke.
3. **Find the user** — `POST /api/v1/search/users`
   (`c1.api.user.v1.UserSearch.Search`) to resolve the subject user id.
4. **Grant** — `POST /api/v1/task/grant`
   (`c1.api.task.v1.TaskService.CreateGrantTask`) with the app entitlement + user.
   **Revoke** — `POST /api/v1/task/revoke`
   (`c1.api.task.v1.TaskService.CreateRevokeTask`).
5. The task then runs through the applicable approval/provision policy; poll it
   with `GET /api/v1/tasks/{id}` (`c1.api.task.v1.TaskService.Get`).

## Notes
- Grant/revoke change live access — high-consequence. Confirm app + entitlement
  + user ids before creating the task.
- Paginate list/search with `page_size` + `page_token`
  (`conventions/c1-conventions.yml`). No idempotency key — re-search rather than
  blind-retry on failure.
