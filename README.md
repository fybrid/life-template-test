# life-template

English | [日本語](README.ja.md)

A template for managing your life with GitHub and an AI agent. Just talk to the agent (Claude Code, Codex, and others) in plain words, and it keeps your tasks and TODO lists for you.

- **Tasks** (things you keep working on for days or weeks) are stored as GitHub Issues and shown on a GitHub Project board and Roadmap.
- **Daily TODOs** (today's, this week's, and this month's lists) are stored as Markdown files on your own computer.

```text
/todo Add "Buy milk"                     → added to today's TODO list
/todo Show today's TODO list
/task 本を買うタスクを追加して              → a GitHub Issue is created and added to the Project
/task #3を完了にして                       → Issue #3 is closed and moved to Done
/task 未完了のタスクをラベル別に見せて
```

No programming is required. You will copy and paste a few settings once during setup.

## What you need

| Item | Notes |
| --- | --- |
| GitHub account | Free plan is fine. [Sign up](https://github.com/signup) |
| An AI agent | [Claude Code](https://claude.com/claude-code) (the desktop app is easiest) or [Codex](https://developers.openai.com/codex). Both need a paid plan. |
| GitHub Desktop (recommended) | Used to copy the repository to your computer. [Download](https://desktop.github.com/) |

## Setup

Setup takes about 20 minutes. You only do it once.

### Step 1. Create your own repository from this template

1. Click **Use this template** → **Create a new repository** at the top of this page.
2. Enter a name (for example, `life`).
3. Select **Private**. Your tasks will be stored here, so keep it private.
4. Click **Create repository**.

> [!IMPORTANT]
> Use **Use this template**, not **Fork**. A fork of a public repository cannot be made private, so your tasks would be visible to everyone.

### Step 2. Copy the repository to your computer

1. Open GitHub Desktop and sign in with your GitHub account.
2. Select **File** → **Clone repository**, choose the repository you created, and click **Clone**.
3. Remember the folder it was saved to (for example, `Documents/GitHub/life`).

If you use Git on the command line, `git clone` works too.

### Step 3. Create a GitHub token

The AI agent uses a token (a password for programs) to work with GitHub on your behalf.

1. Open [New personal access token (classic)](https://github.com/settings/tokens/new).
2. **Note**: enter something like `life agent`.
3. **Expiration**: choose a period. You will create a new token when it expires.
4. **Select scopes**: check **repo** and **project**.
5. Click **Generate token** and copy the token that starts with `ghp_`. It is shown only once.

> [!CAUTION]
> Treat the token like a password. Never paste it into the AI chat, a file in this repository, or anywhere public.

Use a **classic** token. Fine-grained tokens may not work with Projects owned by a personal account.

### Step 4. Save the token on your computer

Save the token as an environment variable named `GITHUB_PAT`. The agent's settings in this repository read it from there.

**macOS**

1. Open the **Terminal** app and run:

   ```bash
   touch ~/.zshrc && open -e ~/.zshrc
   ```

2. In the TextEdit window that opens, add this line at the end, replacing `ghp_xxxx` with your token, and save:

   ```bash
   export GITHUB_PAT="ghp_xxxx"
   ```

**Windows**

1. Open the Start menu and search for **Edit environment variables for your account**.
2. Under **User variables**, click **New**.
3. Enter `GITHUB_PAT` as the name and your token as the value, then click **OK**.

Then **fully quit and restart** your AI agent app so it picks up the new setting.

### Step 5. Open the repository in your AI agent

**Claude Code (desktop app)**: open the folder from Step 2. When asked whether to use the `github` MCP server from `.mcp.json`, approve it.

**Codex**: open the folder and trust the project when asked. The GitHub connection in `.codex/config.toml` is used only in trusted projects.

**Other agents**: this repository's rules are in `AGENTS.md` and the skills are in `.agents/skills/`. Configure the GitHub MCP server (`https://api.githubcopilot.com/mcp/`) with the `GITHUB_PAT` token by following your agent's documentation.

### Step 6. Run the setup skill

Type the following to the agent (`/setup` in Claude Code, `$setup` in Codex):

```text
/setup
```

The agent checks the GitHub connection, creates the labels and the Project, and writes your settings into [docs/tasks.md](docs/tasks.md). Answer its questions as you go. If it asks you to do something on the GitHub website, follow [Create the Project manually](#create-the-project-manually).

When it finishes, commit and push the change to `docs/tasks.md` (ask the agent, or use GitHub Desktop).

### Step 7. Try it

```text
/todo Add "Try the life template"
/task 勉強会の資料を読むタスクを追加して
```

Open your Project on GitHub to see the new task.

## How to use

### TODO and task: which one?

| | TODO (`/todo`) | Task (`/task`) |
| --- | --- | --- |
| For | Things to do today, this week, or this month | Things you keep working on, with progress and dates |
| Stored in | Markdown files in `todo/` on your computer (not uploaded to GitHub) | GitHub Issues and Project |
| Example | Buy milk, send a package | Read a book, prepare for an exam, plan a trip |

Details: [TODO rules](todo/README.md) and [GitHub task policy](docs/tasks.md).

### TODO examples

```text
/todo Add "Send the package" to today's TODO list.
/todo Show this week's TODO list.
/todo Mark "Send the package" as complete.
/todo Review this week.
```

### Task examples

```text
/task コーヒー用品を買うタスクを追加して
/task #5の期限を2026-10-07にして
/task #5を完了にして
/task 未完了のタスクをラベル別に見せて
```

Tasks are labeled automatically (for example, `買い物` for buying or `読書` for reading). Dates appear on the Project's Roadmap view.

## Create the Project manually

Use these steps if the `setup` skill cannot create the Project or its fields.

1. Open your profile → **Projects** → **New project**, choose **Table**, enter a name, and create it.
2. Open the **Status** field settings (column menu → **Edit field**) and make the options exactly: `Backlog`, `Ready`, `In progress`, `Done`, `Cancelled`.
3. Add fields with **+** → **New field**:
   - `Start date` (type: Date)
   - `Target date` (type: Date)
   - `日付種別` (type: Single select, options: `期限`, `予定`)
4. Open **⋯** → **Workflows** and make sure **Item closed** is on and sets Status to `Done`.
5. Optional: add a **Roadmap** view that uses `Start date` and `Target date`.
6. Tell the agent the Project URL and run `/setup` again to finish.

## Customize

- **Labels**: edit the table in [docs/tasks.md](docs/tasks.md#labels), then run `/setup` to create new labels.
- **Issue language**: change `Issue language` in [docs/tasks.md](docs/tasks.md#configuration).
- **Time zone**: dates are resolved in `Asia/Tokyo`. Change it in the skills under `.agents/skills/` and the docs if you live elsewhere.
- **Keep TODO files on GitHub**: remove the matching lines from `.gitignore` (see [todo/README.md](todo/README.md#records)).
- **Add your own record types**: ask the agent with the `records` skill, for example `/records I want to keep a reading log`.
- **Personal agent notes**: put your own instructions in `.codex/LOCAL.md`. It is not uploaded to GitHub.

## Troubleshooting

| Problem | What to do |
| --- | --- |
| The agent says GitHub tools are not available | Check Step 4, then fully quit and restart the agent app. In Claude Code, make sure the `github` server was approved. |
| Authentication error (401) | The token is wrong or expired. Create a new one (Step 3) and update `GITHUB_PAT`. |
| The Project cannot be found or edited | Make sure the token has the **project** scope. |
| Skills are not found on Windows | `.claude/skills` is a symbolic link. Turn on Windows Developer Mode, run `git config --global core.symlinks true`, and clone again. |

## Using GitHub CLI instead of MCP

If you prefer [GitHub CLI](https://cli.github.com/), run `gh auth login` and `gh auth refresh -s project`. When the GitHub MCP tools are unavailable, the `task` skill falls back to `gh`.

## Directory structure

```text
.
├── .agents/skills/     # Skills: setup, task, todo, records
├── .claude/skills      # Symlink to .agents/skills (for Claude Code)
├── .codex/config.toml  # GitHub MCP server for Codex
├── .mcp.json           # GitHub MCP server for Claude Code
├── docs/tasks.md       # GitHub task policy and Configuration
├── todo/               # Local TODO records (daily, weekly, monthly)
├── AGENTS.md           # Rules for AI agents
└── CLAUDE.md           # Loads AGENTS.md for Claude Code
```

## For contributors

System definitions (skills, docs, and AGENTS.md) are written in English. Commit message rules are in [AGENTS.md](AGENTS.md#commit-messages). To use the commit template:

```bash
git config commit.template .github/.gitmessage.txt
```

## References

- [githubで人生を管理する](https://zenn.dev/hand_dot/articles/85c9640b7dcc66)
- [GitHub MCP Server](https://github.com/github/github-mcp-server)
