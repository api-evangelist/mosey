---
name: Onboard a legal entity and generate compliance tasks
description: Authenticate (or sign up) a Mosey legal entity, register the states it operates in, and pull the compliance tasks Mosey generates for those regions.
api: openapi/mosey-openapi.json
operations:
  - account_authenticate_accounts_auth_post
  - embedded_signup_handler_accounts_signup_post
  - add_location_handler_locations__location_public_id__put
  - list_locations_handler_locations_get
  - list_tasks_handler_tasks_get
---

# Onboard a legal entity and generate compliance tasks

Use the Mosey API to bring a business online for multi-state compliance tracking.

## Auth
- Base URL: `https://api.mosey.com`. All calls are OAuth2 password-grant bearer.
- Obtain a token: `POST /api/token`, then send `Authorization: Bearer <token>` on every request.
- To create a brand-new legal entity instead of authenticating an existing one, call
  `embedded_signup_handler_accounts_signup_post` (`POST /accounts/signup`) or authenticate an
  existing one with `account_authenticate_accounts_auth_post` (`POST /accounts/auth`).

## Steps
1. Authenticate the legal entity: `account_authenticate_accounts_auth_post`. Capture the access token.
2. Register each state/region the business operates in: `add_location_handler_locations__location_public_id__put`
   (`PUT /locations/{location_public_id}`). Supply the current full-time employee count so Mosey
   configures the region and generates the applicable tasks.
3. Confirm the registered regions: `list_locations_handler_locations_get` (`GET /locations`).
4. Pull the generated compliance tasks: `list_tasks_handler_tasks_get` (`GET /tasks`). Filter with
   `status`, `definition_id`, `start_date`, `end_date`, `include_managed`, `tags` as needed.

## Rules
- Adding a region is what triggers task generation — expect the task list to populate only after step 2.
- Errors come back as HTTP 422 with `{ "detail": [ { "loc", "msg", "type" } ] }`; read `detail[].loc`
  to find the bad field. A 401 means the token expired — re-run `POST /api/token`.
- There is no cursor pagination; list endpoints are filtered by query parameters.
