---
name: manage-passes
description: Publish Passlet templates and issue personalized Apple Wallet and Google Wallet passes, including digital membership cards, loyalty cards and event tickets. Find, read, update, upgrade or void issued passes and retrieve or revoke add-to-wallet links. Use for individual or batch issuance and changes to holders' passes; not for designing a template's look or organizing projects.
---

# Work with passes

A pass is one holder's copy of a template: its own label, variable values and revision, issued to Apple Wallet, Google Wallet or both. Use the Passlet MCP tools and trust their results over assumptions; every write here reaches real holders' devices.

## Before you write

- Use `get_workspace` when the workspace is unknown. Reading passes needs `passes:read`; issuing and changing them needs `passes:write`; publishing needs `templates:write`. If a call reports a missing permission, explain it and let the user reconnect with it. Never ask for API keys or passwords in chat.
- `issue_passes`, `update_passes`, `void_passes`, `revoke_pass_links` and `publish_template` change what holders see. Before calling one, say what will happen and to how many passes, and wait for the user's go-ahead unless they asked for exactly that. The host may also ask for confirmation.
- Use the holder data the user supplies. Never invent names, member numbers, balances or other facts. Store only what the pass needs to show: Passlet keeps the label and the variables, and everything else stays in the user's own system.

## Publish before issuing

- Passes are issued from a template's **published** version, never from its draft. `list_templates` with `published_only: true` lists the templates that can issue passes. `get_template` shows the published version as `published`, which is `null` until the template is first published. `hasDraft: true` means the design has unpublished changes, which issuing ignores.
- Publish only when the user asks or agrees. Before `publish_template`, read the template with `get_template`: clear every error issue, and tell the user its `publishImpact`. Publishing updates the Google pass class that every pass issued from the template shows at once, and it locks the Apple pass style.
- Name the `revision` from your last `get_template` or successful design write. A refusal because the draft has changed means someone edited it: read it again and review it before publishing.
- Passes already issued keep rendering their version after a publish. `list_passes` with `upgrade_available: true` finds them, and `update_passes` with `template_version: "latest"` moves them (see below). `publish_template` returns the new published version with its variables.

## Issue passes

- Give each pass an `external_id` that is stable and unique in the workspace, such as the member, ticket or order number from the user's system. A second `issue_passes` with the same id returns the pass already issued and changes nothing, even if the label, variables or template differ. That makes retries safe. To change an issued pass, use `update_passes`. To give someone a new pass after voiding the old one, use a new id.
- `label` is the holder's name as the workspace lists it. Take the `variables` from `published.variables` of `get_template`: each variable's key and type, whether it is required, and any default, options or limits. Every required variable needs a value. Variables Passlet fills in itself, such as `scanToken`, are not listed and take no value. Don't go by the design's `content.variables`: while the template has unpublished changes, they can differ from the published ones.
- Issue up to 100 passes per call. A call that partly fails still issues the rest: read each result's `success` and `error`, fix the failed items and resend only those, with the same ids. Issued passes count toward the workspace's plan. A refusal for the plan's pass limit applies to the remaining passes too: stop and report it.
- Issuing is asynchronous. A pass starts `QUEUED`, moves to `ISSUING`, then becomes `ISSUED` (it can be added to a wallet) or `FAILED`. Add-to-wallet links are `null` until it is `ISSUED`. Check with `get_pass` after a short wait instead of calling it in a tight loop. A `FAILED` pass is not retried by these tools; tell the user, as the Passlet console shows why it failed and can retry it.

## Find and read passes

- `list_passes` lists the newest passes first. Filter by `search` (label or external id), `status`, `template_id`, `project_id` (`null` for passes in no project), exact `variables` matches as `key:value`, `upgrade_available`, `redeemed` and creation time. `total` counts every match, and `nextCursor` reads the next page. Take up to 100 per page when the page feeds a batch call.
- `get_pass` reads one pass by `pass_id`, or by `code`: what its barcode encodes, its wallet serial number or its external id. A code shared by several passes is refused; use `list_passes` instead. Its `hostedLinks.smart` link sends each holder to the right wallet; share that unless the user wants a specific one.
- `preview: "apple"` or `"google"` draws the pass with its own values. Use it when the user wants to see a holder's pass. It is an approximation, not a device screenshot.
- `templateVersion` is the version a pass renders, and `latestTemplateVersion` is the newest published one. `revision` counts the changes made to that pass.

## Change passes

- `update_passes` changes up to 100 passes. Each item sends only what changes:
  - `label`
  - `variables`: merged over the stored values; `null` removes one, so the template's default applies again
  - `template_version`: a published version number or `latest`; passes only move forward
- Each change becomes one new revision and is pushed to the wallets that hold the pass. `changed: false` means the pass already matched.
- Send the `revision` you read when edits may race with someone else's. A pass that has changed since is left alone and reported; read it again and reapply the change to its current values.
- To upgrade passes after a publish, page through `list_passes` with `template_id` and `upgrade_available: true`, and send each page to `update_passes` with `template_version: "latest"`. A pass refused with `missingVariables` needs values for variables the new version requires: ask the user, then resend that pass with the version and the `variables` together.
- Report the outcome per pass, as the call returns it. Don't summarize a partly failed batch as done.

## End passes and links

- `void_passes` ends passes for good: they can no longer be scanned or added, and wallets show them as void. It cannot be undone. Confirm which passes, add the user's `reason` when given (webhooks receive it), and treat an already-void pass as done.
- `revoke_pass_links` only invalidates add-to-wallet links handed out so far, for example a link sent to the wrong person. The pass stays valid and stays in wallets that hold it. The next `get_pass` returns new links. Void the pass instead when the holder must lose it.
- Passes can also become `EXPIRED`, when the template's expiration passes; voiding is not needed for those.
