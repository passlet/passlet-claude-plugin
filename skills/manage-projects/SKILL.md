---
name: manage-projects
description: Organize Apple Wallet and Google Wallet pass templates and their issued passes into Passlet projects for events, clients or membership programs. List, create, rename, recolor or delete projects, move templates between projects and report template and pass counts. Use for grouping, sorting or summarizing workspace content; not for designing templates or changing individual passes.
---

# Organize projects

A project groups templates and every pass issued from them, for example one event, venue or client. It only organizes: it has no permissions, design or settings of its own. Projects use the template permissions: `templates:read` to read them and `templates:write` to change them.

## How filing works

- A template is filed in at most one project. Every pass issued from it belongs to the same project; a pass is never filed on its own.
- `move_template` files a template in a project, or takes it out with `project_id: null`, and moves all its passes with it. The result reports `passesMoved`. Before moving a template that has passes, tell the user how many and where they go.
- To file a new template straight away, pass `project_id` to `create_template`.
- `list_templates` and `list_passes` take `project_id` to show what a project holds; for `list_passes`, `project_id: null` lists the passes in no project.

## Read projects

- `list_projects` lists every project with its template count. Use it to find a project's id; match names case-insensitively, and ask when two projects match.
- `get_project` adds the counts of the project: templates, passes in all, live passes (issued or being issued), passes saved in Google Wallet, and passes created in the last 7 days. Report these as counts, not as holder numbers or revenue.

## Change projects

- `create_project` is not idempotent: a repeated call creates a second project with the same name. Check `list_projects` first, and after a failed or uncertain call, check again before retrying.
- `color` takes one of the console palette's names (gray, red, orange, yellow, green, blue, indigo, purple or pink); gray is the default. The colour only marks the project in the Passlet console.
- `update_project` changes only the fields sent; `description: null` removes the description.
- `delete_project` deletes the project and nothing in it: its templates, passes and scans stay, filed in no project. Confirm first. To restore the grouping later, create a project (it gets a new id) and move the templates into it again.
