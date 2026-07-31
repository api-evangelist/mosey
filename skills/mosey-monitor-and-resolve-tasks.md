---
name: Monitor and resolve compliance tasks
description: List a legal entity's open Mosey compliance tasks, inspect a task, and resolve it either by updating its status or by handing the user a hosted resolution session.
api: openapi/mosey-openapi.json
operations:
  - list_tasks_handler_tasks_get
  - get_task_handler_tasks__task_id__get
  - patch_task_handler_tasks__task_id__patch
  - automate_task_handler_tasks__task_id__automate_sessions_post
  - hosted_task_tasks_sessions_post
---

# Monitor and resolve compliance tasks

Keep a business current on its state and local compliance obligations.

## Auth
- Base URL `https://api.mosey.com`; OAuth2 password bearer token from `POST /api/token`.

## Steps
1. List open tasks: `list_tasks_handler_tasks_get` (`GET /tasks`) with `status=todo` (also
   `start_date`/`end_date`/`tags`/`definition_id` filters).
2. Inspect a task: `get_task_handler_tasks__task_id__get` (`GET /tasks/{task_id}`) to read its
   requirements and resolution actions.
3. Resolve it one of two ways:
   - Requirement task: mark it done with `patch_task_handler_tasks__task_id__patch`
     (`PATCH /tasks/{task_id}`). Status can be set to `todo` or `done` only.
   - Needs a guided flow: mint a hosted session with
     `automate_task_handler_tasks__task_id__automate_sessions_post`
     (`POST /tasks/{task_id}/automate/sessions`) and redirect the user to the returned URL. For a
     full tasks dashboard, use `hosted_task_tasks_sessions_post` (`POST /tasks/sessions`).

## Rules
- Question tasks cannot have their status patched directly — answer them via the question-answer
  operation; only requirement tasks accept `todo`/`done`.
- Hosted-session URLs are short-lived and authenticated; hand them to the end user, do not cache.
- Errors are HTTP 422 `{ "detail": [...] }`; 401 means re-authenticate. No idempotency key is
  supported, so avoid blind retries of PATCH operations.
