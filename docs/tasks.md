# GitHub Tasks

Ongoing everyday tasks are Issues in the configured repository and Project, written in the configured Issue language. Use the `task` skill, which operates GitHub through the GitHub MCP server (GitHub CLI `gh` is a fallback). GitHub stores task state; this repository stores the operating rules only. Local daily, weekly, and monthly records remain under `todo/`.

## Configuration

Replace the `<...>` placeholders with your own values. The `setup` skill fills them in and creates the labels and Project fields below; see the README for manual steps. Complete this configuration before using the `task` skill to operate on GitHub.

| Setting | Value |
| --- | --- |
| Issue repository | `<owner>/<repo>` |
| Project owner | `<owner>` |
| Project number | `<project-number>` |
| Project URL | `<project-url>` |
| Issue language | `<language>` (for example, Japanese or English) |
| Labels | Defined in [Labels](#labels); replace that table with your own labels. |
| Project Status options | `Backlog`, `Ready`, `In progress`, `Done`, `Cancelled` |
| Project date fields | `Start date`, `Target date` |
| Project single-select field | `日付種別`: `期限`, `予定` |

Leave Project Priority, Size, and Estimate unchanged unless requested. When the GitHub CLI fallback is used, GitHub CLI 2.101.0 or later is recommended.

## Usage

Invoke the skill as `/task` in Claude Code or `$task` in Codex; other agents may use their own syntax or a plain request.

```text
$task コーヒー用品を買うタスクを追加して
$task #65を完了にして
$task #65を未完了に戻して
$task #65の期限を2026-10-07にして
$task #53の予定を2026-10-01から2026-10-07にして
$task 未完了のタスクをラベル別に見せて
```

GitHub-facing Issue text uses the configured Issue language. Repository documentation and skill instructions remain English. Credentials must not be stored in this repository.

Explicit `$task` requests operate on GitHub; explicit `$todo` requests operate on local records. Dates or Issue links in the content do not change that destination. Without an explicit skill, use the destination established by the request or conversation, or ask when unclear.

## Labels

Automatically choose one primary label based on the action that completes the task, not its subject. Explicit user labels take precedence. Leave uncertain tasks unlabeled and report the uncertainty. New label definitions require a separate proposal/request. Preserve labels during unrelated updates.

| Label | Classification rule |
| --- | --- |
| 読書 | Read books, including technical books. |
| 調査・学習 | Research, study, or evaluate tools. |
| 開発・制作 | Build software, demos, diagrams, or reusable technical artifacts. |
| 環境構築・設定 | Set up environments, integrations, technical account settings, or authentication. |
| 執筆 | Write and publish articles or other prose. |
| 買い物 | Purchase goods, including books and technical equipment. |
| 手続き | Apply, register, request documents, or change administrative/payment arrangements. Includes exam and seminar registration. |
| 生活管理 | Manage finances, health, or personal care. Includes medical/hair appointments and their reservations. |
| イベント・外出 | Attend events, watch films, or visit places. |
| 計画・検討 | Define goals, consider career choices, or develop ideas. |

Examples: buying an AWS book is `買い物`; reading it is `読書`. Trying Raycast is `調査・学習`; configuring an integration is `環境構築・設定`. Seminar registration is `手続き`; attendance is `イベント・外出`.

## Status and lifecycle

- New tasks default to Project `Backlog`.
- Active Project Status options are `Backlog`, `Ready`, and `In progress`.
- `Done` means only an Issue closed with reason `completed`.
- `Cancelled` means an Issue closed with reason `not planned` or `duplicate`.
- Reopening from either closed state means Issue open and Project Status `Backlog`, unless another active status was requested.
- The Project's `Item closed` automation sets Status to `Done` when an Issue closes. After closing as `not planned` or `duplicate`, set Status to `Cancelled` and verify both the Issue reason and Project Status.
- Do not use labels to duplicate progress, priority, or dates. Leave Priority, Size, and Estimate unchanged unless requested.
- Do not synchronize local TODO checkboxes with Issue completion automatically.

## Duplicate checks

Before creating an Issue, compare its meaning with existing Issues rather than relying on exact title matches. Show every plausible match when there are several. Use both Issue state and closure reason and Project Status; an Issue may be absent from the Project or have an outdated Status.

| Existing Issue | Creation rule |
| --- | --- |
| Open, including `Backlog`, `Ready`, or `In progress` | Ask whether to use the existing Issue or create a new one before creating. |
| Closed as `not planned` or `duplicate` (`Cancelled`) | Ask whether to reopen the old Issue (recommended) or create a new one before creating. |
| Closed as `completed` (`Done`) | Create a new Issue without asking; treat it as a recurring task. |

If Issue state, closure reason, and Project Status conflict, report the inconsistency instead of assuming that the Status alone is correct. During lists and reviews, report pairs with potentially matching meaning among open and `Cancelled` Issues as suspected duplicates. Do not close or change them unless requested.

## Dates and Roadmap

Resolve dates in `Asia/Tokyo` and store `YYYY-MM-DD`. Dates are optional. Date count refers to dates requested for scheduling, not dates mentioned as background context.

When creating a task, determine each date's role from the instruction:

| Creation instruction | Start date | Target date | 日付種別 |
| --- | --- | --- | --- |
| One date stated as a deadline (締切, 期限, までに) | Empty | Deadline | 期限 |
| One date stated as a planned date (予定日, 当日, 参加日) | Empty | Planned date | 予定 |
| Two dates forming a planned period (期間, 〜から〜まで) | Planned start | Planned finish | 予定 |
| A start date and a deadline | Start | Deadline | 期限 |
| One scheduling date with no stated role | Empty | Deadline | 期限 |
| No date instruction | Empty | Empty | Empty |

When editing, require an explicit date role: start, deadline, planned date or planned finish, or planned period. Dates alone are insufficient, even if only one endpoint is currently set. Ask what the date refers to before adding, changing, or clearing any scheduling fields. Do not apply the creation rules or their no-role default to edits.

| Edit instruction | Behavior |
| --- | --- |
| Change the start date | Update only Start date; preserve Target date and type. |
| Change the deadline | Update Target date and set type to 期限; preserve Start date. |
| Change the planned date or planned finish | Update Target date and set type to 予定; preserve Start date. |
| Change the planned period | Update both endpoints and set type to 予定. |
| No date instruction | Preserve all dates and type. |
| Explicitly clear all dates | Clear both endpoints and type. |

Examples:

- New task with "2026-10-07": no role is stated, so use that date as the deadline.
- New task "CEATEC参加、予定日は2026-10-14": set Target date to 2026-10-14 and type to 予定.
- New task "10/1から10/5まで旅行準備": set Start date 2026-10-01, Target date 2026-10-05, and type 予定.
- New task "10/1に着手して10/7締切": set Start date 2026-10-01, Target date 2026-10-07, and type 期限.
- Edit "#53を2026-10-07に変更": ask whether this is the start, deadline, or planned date; leave scheduling fields unchanged until answered.
- Edit "#53の開始日を2026-10-03に変更": update only the start.
- Edit "#53の完了予定日を2026-10-10に変更": preserve the start and update the planned finish.

Read existing dates before editing. Reject reversed intervals, including those produced by a single-endpoint edit, and clarify ambiguous dates. Never infer a start date, clear an unmentioned endpoint, or extend an overdue deadline automatically.

The existing Roadmap view uses the Project's date fields. Dates are not copied into Issue bodies. Changes made directly in GitHub are authoritative. Keep `日付種別` consistent when editing dates manually. Legacy dates without a type remain unchanged until their meaning is established.

## Storage and recovery

Do not introduce bidirectional local synchronization. Optional future read-only snapshots must include retrieval time and remain outside Git; refresh from GitHub before writing. Local TODO entries may link to Issues but have independent completion semantics.

Report partial success explicitly: an Issue may exist even when Project insertion or a field update fails. Reuse that Issue URL when resuming. Verify the final Issue state and Project fields before reporting completion.
