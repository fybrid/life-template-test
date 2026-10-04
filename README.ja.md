# life-template

[English](README.md) | 日本語

GitHub と AI エージェントで生活を管理するためのテンプレートです。AI エージェント（Claude Code や Codex など）に普段の言葉で話しかけるだけで、タスクや TODO リストを管理してくれます。

- **タスク**（数日〜数週間かけて取り組むこと）は GitHub Issue として保存され、GitHub Project のボードやロードマップで確認できます。
- **毎日の TODO**（今日・今週・今月のリスト）は、自分のパソコン上の Markdown ファイルに保存されます。

```text
/todo 牛乳を買う を追加して            → 今日の TODO リストに追加
/todo 今日の TODO を見せて
/task 本を買うタスクを追加して          → GitHub Issue を作成して Project に追加
/task #3を完了にして                   → Issue #3 を閉じて Done に移動
/task 未完了のタスクをラベル別に見せて
```

プログラミングの知識は不要です。最初の設定で、いくつかの値をコピー＆ペーストするだけです。

## 必要なもの

| 項目 | 補足 |
| --- | --- |
| GitHub アカウント | 無料プランで使えます。[登録はこちら](https://github.com/signup) |
| AI エージェント | [Claude Code](https://claude.com/claude-code)（デスクトップアプリが簡単です）または [Codex](https://developers.openai.com/codex)。どちらも有料プランが必要です。 |
| GitHub Desktop（推奨） | リポジトリを自分のパソコンにコピーするために使います。[ダウンロード](https://desktop.github.com/) |

## 導入手順

所要時間は 20 分ほどです。最初に一度だけ行います。

### Step 1. テンプレートから自分のリポジトリを作る

1. このページ上部の **Use this template** → **Create a new repository** をクリックします。
2. 名前を入力します（例: `life`）。
3. **Private** を選びます。自分のタスクを保存する場所なので、非公開にしてください。
4. **Create repository** をクリックします。

> [!IMPORTANT]
> **Fork** ではなく **Use this template** を使ってください。公開リポジトリの fork は非公開にできないため、タスクが誰でも見られる状態になってしまいます。

### Step 2. リポジトリを自分のパソコンにコピーする

1. GitHub Desktop を開き、GitHub アカウントでサインインします。
2. **File** → **Clone repository** から作成したリポジトリを選び、**Clone** をクリックします。
3. 保存先のフォルダ（例: `Documents/GitHub/life`）を覚えておきます。

コマンドラインで Git を使える方は `git clone` でもかまいません。

### Step 3. GitHub のトークンを作る

AI エージェントはトークン（プログラム用のパスワード）を使って、あなたの代わりに GitHub を操作します。

1. [New personal access token (classic)](https://github.com/settings/tokens/new) を開きます。
2. **Note**: `life agent` など、分かりやすい名前を入力します。
3. **Expiration**: 有効期限を選びます。期限が切れたら新しいトークンを作り直します。
4. **Select scopes**: **repo** と **project** にチェックを入れます。
5. **Generate token** をクリックし、`ghp_` で始まるトークンをコピーします。トークンは一度しか表示されません。

> [!CAUTION]
> トークンはパスワードと同じように扱ってください。AI とのチャット、このリポジトリ内のファイル、公開の場所には絶対に貼り付けないでください。

**classic** のトークンを使ってください。fine-grained のトークンは、個人アカウントの Project では動かないことがあります。

### Step 4. トークンをパソコンに保存する

トークンを `GITHUB_PAT` という名前の環境変数として保存します。このリポジトリのエージェント設定は、ここからトークンを読み込みます。

**macOS**

1. **ターミナル** アプリを開いて、次のコマンドを実行します。

   ```bash
   touch ~/.zshrc && open -e ~/.zshrc
   ```

2. 開いたテキストエディットの最後に次の 1 行を追加し、`ghp_xxxx` を自分のトークンに置き換えて保存します。

   ```bash
   export GITHUB_PAT="ghp_xxxx"
   ```

**Windows**

1. スタートメニューで **環境変数を編集**（アカウントの環境変数を編集）を検索して開きます。
2. **ユーザー環境変数** の **新規** をクリックします。
3. 変数名に `GITHUB_PAT`、変数値にトークンを入力して **OK** をクリックします。

保存したら、AI エージェントのアプリを **完全に終了して再起動** してください。再起動すると新しい設定が読み込まれます。

### Step 5. AI エージェントでリポジトリを開く

**Claude Code（デスクトップアプリ）**: Step 2 のフォルダを開きます。`.mcp.json` の `github` MCP サーバーを使うか確認されたら、許可します。

**Codex**: フォルダを開き、プロジェクトを信頼するか聞かれたら信頼します。`.codex/config.toml` の GitHub 接続は、信頼したプロジェクトでのみ使われます。

**その他のエージェント**: このリポジトリのルールは `AGENTS.md`、スキルは `.agents/skills/` にあります。各エージェントの説明に従って、GitHub MCP サーバー（`https://api.githubcopilot.com/mcp/`）を `GITHUB_PAT` のトークンで設定してください。

### Step 6. setup スキルを実行する

エージェントに次のように入力します（Claude Code では `/setup`、Codex では `$setup`）。

```text
/setup
```

エージェントが GitHub との接続を確認し、ラベルと Project を作成して、設定を [docs/tasks.md](docs/tasks.md) に書き込みます。途中の質問に答えてください。GitHub の画面での操作を頼まれたら、[Project を手動で作る](#project-を手動で作る) を見ながら進めてください。

終わったら、`docs/tasks.md` の変更をコミットしてプッシュします（エージェントに頼むか、GitHub Desktop を使います）。

### Step 7. 使ってみる

```text
/todo 「life テンプレートを試す」を追加して
/task 勉強会の資料を読むタスクを追加して
```

GitHub で自分の Project を開くと、追加したタスクが表示されます。

## 使い方

### TODO とタスクの使い分け

| | TODO（`/todo`） | タスク（`/task`） |
| --- | --- | --- |
| 用途 | 今日・今週・今月やること | 進み具合や日付を管理しながら続けて取り組むこと |
| 保存先 | パソコン上の `todo/` 内の Markdown ファイル（GitHub にはアップロードされません） | GitHub Issue と Project |
| 例 | 牛乳を買う、荷物を送る | 本を読む、試験の準備、旅行の計画 |

詳しくは [TODO のルール](todo/README.md) と [GitHub タスクのルール](docs/tasks.md) を見てください。

### TODO の例

```text
/todo 今日の TODO に「荷物を送る」を追加して
/todo 今週の TODO を見せて
/todo 「荷物を送る」を完了にして
/todo 今週を振り返って
```

### タスクの例

```text
/task コーヒー用品を買うタスクを追加して
/task #5の期限を2026-10-07にして
/task #5を完了にして
/task 未完了のタスクをラベル別に見せて
```

タスクには自動でラベルが付きます（買うなら `買い物`、読むなら `読書` など）。日付は Project の Roadmap ビューに表示されます。

## Project を手動で作る

`setup` スキルで Project やフィールドを作れなかった場合は、次の手順で作ります。

1. 自分のプロフィール → **Projects** → **New project** を開き、**Table** を選んで名前を入力し、作成します。
2. **Status** フィールドの設定（列メニュー → **Edit field**）を開き、選択肢を `Backlog`、`Ready`、`In progress`、`Done`、`Cancelled` の 5 つにします。名前は完全に一致させてください。
3. **+** → **New field** で次のフィールドを追加します。
   - `Start date`（種類: Date）
   - `Target date`（種類: Date）
   - `日付種別`（種類: Single select、選択肢: `期限`、`予定`）
4. **⋯** → **Workflows** を開き、**Item closed** がオンで、Status を `Done` にする設定になっていることを確認します。
5. 任意: `Start date` と `Target date` を使う **Roadmap** ビューを追加します。
6. エージェントに Project の URL を伝えて、もう一度 `/setup` を実行すると設定が完了します。

## カスタマイズ

- **ラベル**: [docs/tasks.md](docs/tasks.md#labels) の表を編集してから `/setup` を実行すると、新しいラベルが作られます。
- **Issue の言語**: [docs/tasks.md](docs/tasks.md#configuration) の `Issue language` を変更します。
- **タイムゾーン**: 日付は `Asia/Tokyo` で扱います。海外で使う場合は、`.agents/skills/` 内のスキルとドキュメントを変更してください。
- **TODO ファイルも GitHub で管理したい**: `.gitignore` の該当行を削除します（[todo/README.md](todo/README.md#records) を参照）。
- **記録の種類を増やしたい**: `records` スキルでエージェントに頼みます。例: `/records 読書記録をつけたい`
- **自分用のメモ**: エージェントへの個人的な指示は `.codex/LOCAL.md` に書けます。このファイルは GitHub にアップロードされません。

## 困ったとき

| 症状 | 対処 |
| --- | --- |
| GitHub のツールが使えないと言われる | Step 4 を確認し、エージェントのアプリを完全に終了して再起動してください。Claude Code では `github` サーバーを許可したかも確認してください。 |
| 認証エラー（401） | トークンが間違っているか、期限切れです。新しいトークンを作り（Step 3）、`GITHUB_PAT` を更新してください。 |
| Project が見つからない、編集できない | トークンに **project** のスコープが付いているか確認してください。 |
| Windows でスキルが見つからない | `.claude/skills` はシンボリックリンクです。Windows の開発者モードをオンにし、`git config --global core.symlinks true` を実行してから clone し直してください。 |

## MCP ではなく GitHub CLI を使う場合

[GitHub CLI](https://cli.github.com/) を使いたい場合は、`gh auth login` と `gh auth refresh -s project` を実行してください。GitHub MCP のツールが使えないときは、`task` スキルが自動で `gh` を使います。

## ディレクトリ構成

```text
.
├── .agents/skills/     # スキル: setup, task, todo, records
├── .claude/skills      # .agents/skills へのシンボリックリンク（Claude Code 用）
├── .codex/config.toml  # Codex 用の GitHub MCP サーバー設定
├── .mcp.json           # Claude Code 用の GitHub MCP サーバー設定
├── docs/tasks.md       # GitHub タスクのルールと設定値
├── todo/               # ローカルの TODO 記録（日・週・月）
├── AGENTS.md           # AI エージェント向けのルール
└── CLAUDE.md           # Claude Code 用に AGENTS.md を読み込む
```

## 仕組みを変更する方へ

スキル、ドキュメント、AGENTS.md などの仕組みの定義は英語で書いています。コミットメッセージのルールは [AGENTS.md](AGENTS.md#commit-messages) にあります。コミットテンプレートを使う場合は、次を実行します。

```bash
git config commit.template .github/.gitmessage.txt
```

## 参考

- [githubで人生を管理する](https://zenn.dev/hand_dot/articles/85c9640b7dcc66)
- [GitHub MCP Server](https://github.com/github/github-mcp-server)
