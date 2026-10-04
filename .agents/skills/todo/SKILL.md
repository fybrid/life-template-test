---
name: todo
description: Record, list, review, update, or complete local daily, weekly, and monthly TODO items in this repository. GitHub Issue and Project tasks belong to the task skill instead.
---

# TODO

Explicit `$todo` or `/todo` selects local period records; explicit `$task` or `/task` selects GitHub Issue/Project operations. Issue references and dates in the content never override the selected skill. In a `$todo` request, an Issue reference is a related link, not authorization to modify GitHub. Without an explicit skill, follow the destination established by the request or conversation, and ask if unclear. The default-to-daily rule below applies after this local workflow has been selected, including explicit `$todo` or `/todo` requests.

Manage TODO items at three time scopes:

- Daily: `todo/daily/YYYY-MM-DD.md`
- Weekly: `todo/weekly/YYYY-Www.md`, using the ISO week-numbering year and a Monday-through-Sunday week
- Monthly: `todo/monthly/YYYY-MM.md`

Resolve dates and periods in `Asia/Tokyo`. Route "today", "tomorrow", and explicit dates to daily; "this week", "next week", and explicit weeks to weekly; and "this month", "next month", and explicit months to monthly. Ask for clarification when the intended scope or period cannot be determined safely.

## Recording

- When a recording request does not specify a scope or period, record it in the daily file for the current date without asking for clarification.
- Show the resolved scope and date, week, or month in the response.
- Create the target directory when it does not exist.
- Create the target file when it does not exist, using this format:

```markdown
# Period

## TODO

- [ ] Concise English task
  - Japanese: Concise Japanese task
```

- Add one bilingual item per distinct task under `## TODO`. The checkbox belongs to the English line; the indented Japanese line is the same task, not a separate item.
- Normalize the request into a concise, actionable TODO in both English and Japanese while preserving names, numbers, deadlines, URLs, and essential details.
- If the user's wording is already clear and actionable, preserve that wording in its original language and translate only the other language. Otherwise, rewrite both versions for clarity. Do not retain unclear wording merely because it is the original text.
- Preserve the user's task order and all unrelated existing content.
- Do not add a task that is already present with the same meaning in either language for that period.
- Do not invent deadlines, priorities, categories, or details.

## Listing

- When the user asks to see a period's TODO list, show every task with its current checkbox state and preserve its order.
- Display the Japanese version when responding in Japanese and the English version when responding in English. Show both only when requested.
- Filter to incomplete or completed tasks only when the user asks for that filter.
- When the target file does not exist, report that no TODO items are recorded for the resolved period. Do not create an empty file merely to list it.
- For an overview request, list the current monthly, weekly, and daily records in that order without merging or duplicating their items.

## Updating

- When the user explicitly says a task is complete, change only its checkbox from `[ ]` to `[x]`; keep both language lines unchanged.
- Match update requests against both language versions. Identify the target scope, period, and task before changing it, and ask for clarification if multiple items plausibly match.
- When editing a task's wording, treat the English checkbox line and its Japanese line as one item. Keep its checkbox state and position, update both languages to the same meaning, and apply the recording rules for preserving or normalizing the user's new wording.
- When removing a task, remove both language lines as one item.
- Do not delete completed tasks unless the user explicitly requests deletion.
- Carry or expand tasks into another period only when explicitly requested. By default, copy only incomplete tasks, leave the source file unchanged, and avoid duplicates in the destination file.
- Do not automatically synchronize completion state between daily, weekly, and monthly files.

## Review

- For a review request, use the requested scope and period. When neither is specified, review the daily file for the current date.
- For an overview review, inspect the current monthly, weekly, and daily files in that order.
- Report incomplete and completed tasks separately, preserve their order, and include counts for each group.
- Treat incomplete tasks as carryover candidates, but do not copy, edit, complete, or delete anything during a review unless the user explicitly requests that action.
- When the user explicitly requests carryover without naming a destination, use the next period of the same scope: the next day, ISO week, or calendar month.

## Verification

After editing, inspect the affected file and report the resolved date, week, or month, the added or changed tasks, and the file path. Do not commit or push unless explicitly requested.
