---
name: task
description: Manage ongoing everyday tasks as GitHub Issues and Project items through the GitHub MCP server, including labels, lifecycle, and Roadmap dates. Use for GitHub tasks, not local daily, weekly, or monthly TODO records.
---

# GitHub Tasks

Read [the task policy](../../../docs/tasks.md), especially [Configuration](../../../docs/tasks.md#configuration), [Labels](../../../docs/tasks.md#labels), [Status and lifecycle](../../../docs/tasks.md#status-and-lifecycle), [Duplicate checks](../../../docs/tasks.md#duplicate-checks), and [Dates and Roadmap](../../../docs/tasks.md#dates-and-roadmap). Take the repository, Project owner, number, and URL from Configuration. If any required value is still a placeholder such as `<owner>`, stop and suggest the `setup` skill before any GitHub operation. GitHub is the source of truth; do not maintain a local editable mirror. Resolve dates in `Asia/Tokyo`.

## Tools

Use the tools of the GitHub MCP server named `github` (toolsets `context`, `issues`, `labels`, and `projects`). Tool names can differ between server versions; choose them by capability, such as reading, creating, updating, or closing Issues, and listing, adding, or updating Project items and fields.

If the GitHub MCP tools are unavailable, check whether GitHub CLI is installed and authenticated (`gh auth status`) and use it instead with the commands in [GitHub CLI fallback](#github-cli-fallback). If neither works, stop and point the user to the README setup steps. Never ask the user to paste a token into the conversation or a repository file.

## Routing and authorization

- Explicit `$task` or `/task` selects GitHub Issue/Project operations; explicit `$todo` or `/todo` selects local TODO operations. Dates and Issue references in the content never override the selected skill.
- Without an explicit skill, follow the destination established by the request or conversation. Ask if the destination remains unclear.
- A request to add or update a task authorizes that operation; do not ask again for routine classification. Reviews and classification proposals are read-only unless changes are requested.
- Write Issue titles, bodies, and any requested comments in the configured Issue language, preserving proper names, URLs, and code. Do not invent details, deadlines, priorities, or completion claims. Comments are not required for routine updates.

## Read and identify

List Issues in all states with their number, title, body, state, closure reason, labels, and URL for an overview; read a single Issue for a target. List Project items with their Status and date fields, and list Project fields to find field and option IDs. The Issue number and Project number are different values.

Treat Issue state and closure reason separately from Project Status. Project membership is not a complete repository Issue list; archived or nonmember Issues can be absent. Follow pagination until all pages are read before claiming completeness. If several Issues could match an update request, show all candidates and ask which one to use.

Before creating, search all Issue states for the same meaning in titles and bodies. Apply [Duplicate checks](../../../docs/tasks.md#duplicate-checks), show every plausible match, and pause for the required choice. For lists and reviews, report suspected duplicate pairs among open and `Cancelled` Issues without changing them.

## Create

1. Select a concise title in the configured Issue language and a body containing only supplied details; an empty body is acceptable. Choose the primary label by [Labels](../../../docs/tasks.md#labels), unless the user specifies labels.
2. After the duplicate check permits creation, create the Issue in the configured repository with the label when one is known; omit the label if uncertain.
3. Capture the returned Issue URL and number immediately. Add the Issue to the configured Project.
4. Set Status to `Backlog` unless another active status was requested. Set dates only as specified in [Dates and Roadmap](../../../docs/tasks.md#dates-and-roadmap).
5. Read back the Issue and Project item and report the URL, label, Status, and any dates.

## Update

- Edit only requested attributes. Preserve existing labels on unrelated edits; do not reclassify a task merely because it is being completed or rescheduled.
- For a requested Project operation on a nonmember Issue, add the existing Issue first. Do not create a replacement Issue.
- On a requested reclassification, replace only the previous taxonomy label; preserve unrelated labels unless removal is requested. Explicit user label choices override the automatic primary label. Propose missing label definitions rather than inventing them during a task operation.
- To complete, close the Issue with state reason `completed`. Read Project Status after the close; set it to `Done` if needed, then verify Issue state, reason, and Status.
- To cancel, close the Issue with state reason `not_planned`. After the close, set Status to `Cancelled`, then read back Issue state, reason, and Status.
- To mark an unwanted Issue as a duplicate, identify the Issue to keep and close the unwanted Issue with state reason `duplicate`, pointing to the kept Issue. After the close, set Status to `Cancelled`, then read back Issue state, reason, and Status.
- For either `Cancelled` close, the `Item closed` automation may set Status to `Done`. If the final read shows `Done`, set `Cancelled` once more and read back again. If state, reason, and Status still disagree, report the mismatch. Do not change automation settings.
- To reopen from `Done` or `Cancelled`, reopen the Issue, set Status to `Backlog` or another explicitly requested active Status, then verify Issue state and Status. A request to move a closed Issue to an active Status requires the same paired reopen and Status update.

## Project fields and dates

Update Project fields by field name: `Status` and `日付種別` are single-select fields set by option, and `Start date` and `Target date` are date fields set as `YYYY-MM-DD` or cleared. Look up field and option IDs from the Project field list when a tool requires IDs.

Use [Dates and Roadmap](../../../docs/tasks.md#dates-and-roadmap) for creation defaults, required edit roles, field changes, and interval validation. Read existing dates before editing. If an edit's date role is unclear, ask before changing or clearing scheduling fields. Read back all affected date fields and `日付種別` after editing.

## Partial failures and verification

Issue creation, Project membership, and field updates are separate operations, not a transaction. On failure, read current state, report which operations succeeded, and stop if permissions or target identity remain unresolved. Never retry Issue creation blindly; recover with its captured URL or search. Resume only missing operations, with at most one retry after resolving the cause.

Read back all affected attributes. A successful Issue close alone does not prove Status synchronization succeeded. Report inconsistencies without overwriting unrelated changes. Existing Project automation may perform part of an update; check its result before repeating it.

## GitHub CLI fallback

Use these only when the GitHub MCP tools are unavailable. Replace placeholders with Configuration values, and use an argument array or safely quoted arguments and a temporary UTF-8 file for multiline text.

```sh
gh issue list --repo <repo> --state all --limit 1000 --json number,title,body,state,stateReason,labels,url
gh issue view <issue-number> --repo <repo> --json number,title,body,state,stateReason,labels,url
gh issue create --repo <repo> --title <title> --body-file <file> --label <label>
gh issue close <issue-number> --repo <repo> --reason completed
gh issue close <issue-number> --repo <repo> --reason "not planned"
gh issue close <issue-number> --repo <repo> --duplicate-of <kept-issue-number>
gh issue reopen <issue-number> --repo <repo>
gh project item-list <number> --owner <owner> --limit 1000 --format json
gh project field-list <number> --owner <owner> --format json
gh project item-add <number> --owner <owner> --url <issue-url> --format json
gh project item-edit <number> --owner <owner> --url <issue-url> --field Status --value Cancelled
gh project item-edit <number> --owner <owner> --url <issue-url> --field 'Target date' --date YYYY-MM-DD
gh project item-edit <number> --owner <owner> --url <issue-url> --field 'Start date' --clear
gh project item-edit <number> --owner <owner> --url <issue-url> --field '日付種別' --value '期限'
```

Project access through GitHub CLI requires `gh auth refresh -s project`. If a list reaches its limit, use paginated `gh api` before claiming completeness.
