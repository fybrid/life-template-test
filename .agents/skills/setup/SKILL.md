---
name: setup
description: Configure this repository for GitHub tasks for the first time, or check the configuration later. Verifies the GitHub MCP connection, creates the task labels and the GitHub Project with its fields, and fills in the task Configuration. Use once after creating a repository from this template, or when task operations fail because of configuration.
---

# Setup

Configure the GitHub side of the `task` skill as defined in [the task policy](../../../docs/tasks.md). Talk to the user in their language and explain each step briefly; the user may not be an engineer. A request to run setup authorizes creating the labels, Project, and fields listed below and editing the Configuration table. Ask before anything else that changes GitHub, and never delete or rename existing labels, Projects, fields, or Issues.

Use the GitHub MCP server named `github` (see the `task` skill's Tools section). Never ask the user to paste a token into the conversation or a repository file.

## 1. Check the connection

1. Call the GitHub MCP tool that returns the authenticated user. Report the login name.
2. If the GitHub tools are missing or authentication fails, explain the likely cause and point to the README setup steps: the `GITHUB_PAT` environment variable is not set, the agent was started before it was set, or the token lacks the `repo` or `project` scope. Stop until the user has fixed it. Use GitHub CLI only if the user prefers it and `gh auth status` shows it is authenticated with the `project` scope.

## 2. Identify the repository

1. Read `git remote get-url origin` and derive `<owner>/<repo>`. If there is no remote, explain that the repository must be pushed to GitHub first and stop.
2. Confirm the repository with the user and check that it is reachable through the GitHub tools.

## 3. Choose the Issue language

Ask which language Issue titles and bodies should use. Suggest the language the user is writing in.

## 4. Create labels

Compare the [Labels](../../../docs/tasks.md#labels) table with the repository's labels. Create only the missing labels, with the classification rule as the description. Leave other existing labels unchanged. If the user wants different labels, update the table first, then create them.

## 5. Create or select the Project

1. Ask whether to create a new Project or use an existing one owned by the user. Suggest the title `<repo> roadmap` for a new one.
2. Ensure these fields exist, creating or adjusting only what is missing:
   - `Status` (single select): `Backlog`, `Ready`, `In progress`, `Done`, `Cancelled`
   - `Start date` (date)
   - `Target date` (date)
   - `日付種別` (single select): `期限`, `予定`
3. If a step cannot be done with the available tools, give the user the matching manual steps from the README section "Create the Project manually", wait until they report completion, then continue.
4. Link the Project to the repository if the tools allow it; otherwise mention the optional manual step.
5. The built-in `Item closed` workflow, which sets Status to `Done`, is enabled by default and cannot be checked through the API. Ask the user to confirm it in the Project's Workflows menu.
6. List the Project fields and verify every field and option name exactly, including spelling and case.

## 6. Write the Configuration

Replace the placeholders in [Configuration](../../../docs/tasks.md#configuration) with the repository, Project owner, Project number, Project URL, and Issue language. Do not change other rows. Show the resulting table.

## 7. Smoke test (optional)

Offer a test run. If the user agrees, create an Issue titled as a setup test in the configured Issue language, add it to the Project with Status `Backlog`, read it back, then close it as `not_planned`, set Status to `Cancelled`, and read back again. Report each result.

## 8. Report

Summarize what was created, verified, and left for the user. Propose committing the Configuration change with `:wrench: Configure GitHub task settings`; commit or push only when the user asks.
