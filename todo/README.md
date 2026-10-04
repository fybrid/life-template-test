# TODO

This directory stores daily, weekly, and monthly TODO records.

Ongoing GitHub Issue tasks use the separate `task` skill and [GitHub task policy](../docs/tasks.md). Explicit `$todo` selects local records, and explicit `$task` selects GitHub operations. Issue links and dates do not override that choice. An Issue's deadline does not make it a local daily TODO. When an unqualified request has no established destination, clarify the destination first; the default-to-daily rule applies within the local `todo` workflow.

## Records

| Scope | Path | Date rule |
| --- | --- | --- |
| Daily | `daily/YYYY-MM-DD.md` | Calendar date |
| Weekly | `weekly/YYYY-Www.md` | ISO week, Monday through Sunday |
| Monthly | `monthly/YYYY-MM.md` | Calendar month |

Record files in these directories are local-only and ignored by Git by default. To version and share a scope, remove its ignore rule and `.gitkeep` exception from `.gitignore`.

## Usage

Use the repository's `todo` skill with a natural-language request. Invoke it as `/todo` in Claude Code or `$todo` in Codex:

```text
$todo Add "Send the package" to today's TODO list.
$todo Add "Buy milk".
$todo Show today's TODO list.
$todo Mark "Send the package" as complete.
$todo Add "Prepare presentation notes" to this week's TODO list.
$todo Show this month's TODO list.
$todo Review this week.
$todo Review my current TODO overview.
```

## Recording Rules

- Resolve dates and periods in the `Asia/Tokyo` time zone.
- Record an item in today's daily file when a recording request does not specify a scope or period.
- Add one bilingual Markdown item per task under `## TODO`:

```markdown
- [ ] Concise English task
  - Japanese: Concise Japanese task
```

- Express the same actionable meaning in English and Japanese.
- If the user's wording is already a clear and actionable TODO, preserve it in that language and translate the other language. Otherwise, normalize both versions into a clear TODO without inventing details.
- Keep completed tasks in their period record unless deletion is explicitly requested.
- When editing task wording, update the English and Japanese lines together while preserving the checkbox state and task order.
- When removing a task, remove both language lines together.
- Carry unfinished tasks to another period only when explicitly requested.
- Do not automatically synchronize completion state across daily, weekly, and monthly files.

## Reviews

- A review separates incomplete and completed tasks and reports their counts without changing the files.
- A review without a scope uses today's daily record.
- An overview review covers the current monthly, weekly, and daily records in that order.
- Incomplete tasks are carryover candidates. They are copied only when explicitly requested, and the source record remains unchanged.
- If a carryover destination is omitted, use the next period of the same scope.
