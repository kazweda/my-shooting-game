# 開発手順書

## PR のマージ方法

このプロジェクトでは、GitHub CLI (`gh`) を使用してPRをマージします。

### 基本的なマージコマンド

```bash
gh pr merge <PR_NUMBER> --merge --delete-branch
```

**パラメータ説明:**
- `<PR_NUMBER>`: マージするPRの番号（例：123）
- `--merge`: マージコミットを作成（デフォルト）
- `--delete-branch`: マージ後に対象ブランチを削除

### 使用例

```bash
# PR #123 をマージして、ブランチを削除
gh pr merge 123 --merge --delete-branch
```

### その他のマージ戦略

**Squash マージ:**
```bash
gh pr merge <PR_NUMBER> --squash --delete-branch
```

**Rebase マージ:**
```bash
gh pr merge <PR_NUMBER> --rebase --delete-branch
```

### PR の状態確認

```bash
# PR の詳細情報を表示
gh pr view <PR_NUMBER>

# すべてのオープン PR を一覧表示
gh pr list
```

### PR のレビュー

```bash
# PR をレビュー中としてマーク
gh pr review <PR_NUMBER> --request-changes

# PR を承認
gh pr review <PR_NUMBER> --approve

# コメント付きでレビュー
gh pr review <PR_NUMBER> --comment --body "レビューコメント"
```

### Copilot レビューコメントの取得

**JSON 形式で全レビューコメントを取得:**
```bash
gh pr view <PR_NUMBER> --json reviews
```

#### ファイルレベルのレビューコメント（suggestions含む）を取得
コード差分に対するコメント（path/line付き）は `gh api` を使用します。

一般形:
```bash
gh api repos/<OWNER>/<REPO>/pulls/<PR_NUMBER>/comments --paginate \
	| jq '.[] | {user: .user.login, path, line, body}'
```

このリポジトリの例:
```bash
gh api repos/kazweda/flutter-web-sandbox/pulls/<PR_NUMBER>/comments --paginate \
	| jq '.[] | {user: .user.login, path, line, body}'
```

補足:
- `pulls/<num>/comments` はコードレビューの「行に紐づくコメント」を返します（一般的なPRコメントとは別API）。
- `--paginate` を付けると大量コメントでも全件取得できます。
- `jq` で `path` と `line` を含むコメントのみ抽出し、Copilotなどのユーザーでフィルタ可能です。

## 参考資料

- [GitHub CLI Documentation](https://cli.github.com/manual/)
- [gh pr merge](https://cli.github.com/manual/gh_pr_merge)
