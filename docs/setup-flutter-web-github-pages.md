# Flutter Web を GitHub Pages にデプロイする手順メモ

このドキュメントは、Flutter Web アプリを  
**GitHub Pages + GitHub Actions** で公開するまでの実践メモです。

検証・学習用途（sandbox）を想定しています。

---

## TL;DR（結論）

- リポジトリ名: `flutter-web-sandbox`
- Dart package 名: `flutter_web_sandbox`
- デプロイ方式: GitHub Actions → `gh-pages` ブランチ
- GitHub Pages 設定: **Deploy from a branch**
- `--base-href` は必須（repo 名と一致させる）

---

## 1. リポジトリ作成方針

### リポジトリ名
```text
flutter-web-sandbox
```

- GitHub Pages の URL に使われる
- kebab-case（`-`）を使うのが自然

### Dart / Flutter のパッケージ名
```text
flutter_web_sandbox
```

- Dart は **ハイフン不可**
- snake_case が必須
- repo 名と一致させる必要はない

---

## 2. Flutter プロジェクト作成

### 推奨コマンド（package 名を明示）

```bash
flutter create \
  --project-name flutter_web_sandbox \
  flutter-web-sandbox

cd flutter-web-sandbox
```

---

## 3. Git リポジトリ初期化 & GitHub 連携（SSH）

```bash
git init
git branch -M main
git remote add origin git@github.com:ユーザー名/flutter-web-sandbox.git
git add .
git commit -m "Initial Flutter project"
git push -u origin main
```

※ SSH 利用で問題なし（推奨）

---

## 4. Flutter Web が有効か確認

```bash
flutter config --enable-web
flutter devices
```

以下が表示されれば OK：

- Chrome
- Web Server

### ローカル実行
```bash
flutter run -d chrome
```

---

## 5. GitHub Actions（自動デプロイ）

### workflow ファイル作成

```text
.github/workflows/deploy.yml
```

### deploy.yml（最小構成）

```yaml
name: Deploy Flutter Web to GitHub Pages

on:
  push:
    branches:
      - main

permissions:
  contents: write

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - uses: subosito/flutter-action@v2
        with:
          channel: stable
          cache: true

      - run: flutter pub get

      - run: flutter build web --release --base-href /flutter-web-sandbox/

      - run: cp build/web/index.html build/web/404.html

      - uses: peaceiris/actions-gh-pages@v4
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: build/web
```

```bash
git add .github/workflows/deploy.yml
git commit -m "Add GitHub Actions deploy for Flutter web"
git push
```

---

## 6. GitHub Pages 設定

### 初回状態
```
Settings → Pages
Source: Deploy from a branch
Branch: None
```

→ **これは正常**

### Actions 成功後
- `gh-pages` ブランチが自動生成される
- Pages 設定で以下を選択

```
Source: Deploy from a branch
Branch: gh-pages
Folder: / (root)
```

---

## 7. 公開 URL

```text
https://ユーザー名.github.io/flutter-web-sandbox/
```

---

## 8. よくあるトラブル

### 画面が真っ白
- `--base-href` が間違っている
- リポジトリ名と一致していない

### Branch が選べない
- Actions がまだ一度も成功していない
- Pages 設定画面の再読み込み不足

---

## 9. 補足メモ

### `.code-workspace` を gitignore に書かず無視する

```text
.git/info/exclude
```

```text
*.code-workspace
```

---

## まとめ

- repo 名（kebab-case）と Dart package 名（snake_case）は別物
- GitHub Pages は **gh-pages ブランチを配信**
- Actions + Pages の役割分担を理解すると迷わない
- base-href を忘れなければ成功率は高い
