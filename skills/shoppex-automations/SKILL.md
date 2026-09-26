---
name: shoppex-automations
description: Design, test and activate Shoppex automations (workflows) safely with the workflows_* tools, from merchant goal to approved active workflow.
---

# Shoppex automations

An active workflow runs on real events without asking anyone: it can issue
store credit, send coupons or blacklist customers. The safe order below is
what keeps a draft from hurting a live store.

## The order

1. `workflows_catalog`: read the live triggers, condition fields, operators,
   actions, variables and limits. Use only keys from the catalog; never
   invent one. `workflows_templates` has starting points.
2. Write a definition: trigger, conditions (`all` or `any`), steps and
   settings (`run_limit`, `max_runs_per_hour`). Ask the merchant for any
   choice you cannot read from their goal, such as amounts or wording.
3. `workflows_validate`: fix every issue at the field path it names.
4. `workflows_test`: dry-run with `{ "kind": "example" }`, `latest` or a
   real `order`. It plans steps only and changes nothing. Never say a dry
   run did something.
5. `workflows_create`: save a DRAFT with an `idempotency_key`. Reuse the key
   if you retry after a timeout, or you get a second draft.
6. Show the merchant the plan: trigger, conditions, each step's effect, run
   limits and the draft ID.
7. Only after the merchant says to turn it on: `workflows_set_status` with
   `ACTIVE`. The original goal is not approval.

## Changing and stopping

- `workflows_update` on an active workflow affects future runs. Validate
  and test the new definition first, then confirm.
- `workflows_set_status` with `PAUSED` stops new runs;
  `cancel_waiting_runs: true` also cancels runs that are waiting.
- `workflows_delete` removes the workflow and its history. Confirm the name
  with the merchant first.
- `workflow_runs_retry` repeats real side effects of a failed run. Confirm
  first. `workflow_runs_cancel` does not undo steps that already ran.

## Reviewing

`workflows_report` gives totals, skip reasons, success rates and the top
failure messages for 1 to 90 days. Use `workflow_runs_list` with a
`status` filter to find the runs behind a number.
