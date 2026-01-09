# Copilot Instructions for flutter-web-sandbox

- **App shape**: Single Flutter counter app rooted at [lib/main.dart](../lib/main.dart) using `MaterialApp` + `MyHomePage` stateful counter. No additional routes, services, or data sources yet.
- **Theme choices**: Material 3 with `colorScheme` seeded from deep purple; `AppBar` uses `inversePrimary`. Keep UI changes consistent with this simple palette unless intentionally redesigning.
- **State flow**: `_counter` is the only mutable state, updated via `setState` in `_incrementCounter`. If adding features, prefer keeping state colocated in widgets until complexity requires a state manager.
- **Dependencies**: Minimal: `flutter`, `cupertino_icons`; dev: `flutter_test`, `flutter_lints` (see [pubspec.yaml](../pubspec.yaml)). Add packages via `flutter pub add <pkg>` and keep `publish_to: none` unless changing release intent.
- **Linting**: Uses `package:flutter_lints` defaults ([analysis_options.yaml](../analysis_options.yaml)); no custom rules. Run `flutter analyze` before PRs when touching Dart.
- **Testing**: Default widget test scaffold lives in [test/widget_test.dart](../test/widget_test.dart). Use `flutter test` locally; add focused widget tests for UI changes.
- **Local dev**: Ensure web is enabled (`flutter config --enable-web`). Run `flutter run -d chrome` for live testing.
- **Build (web)**: Use `flutter build web --release --base-href /flutter-web-sandbox/` so assets resolve correctly on GitHub Pages (path baked into workflow below). Copy `build/web/index.html` to `build/web/404.html` to support SPA routing.
- **CI/CD**: GitHub Actions workflow [.github/workflows/deploy.yml](./workflows/deploy.yml) builds on pushes to `main`, using `subosito/flutter-action@v2` (stable, cached), builds web with base href, copies 404, and publishes `build/web` to `gh-pages` via `peaceiris/actions-gh-pages@v4`. Pages settings expect source `gh-pages` / root.
- **Branching**: Default + working branch is `main`; Actions deploy only from `main`.
- **PR merge workflow**: Repository expects GitHub CLI usage: `gh pr merge <PR> --merge --delete-branch` (see [docs/DEVELOPMENT.md](../docs/DEVELOPMENT.md) for squash/rebase variants and review commands).
- **Setup notes**: Project created via `flutter create --project-name flutter_web_sandbox flutter-web-sandbox`; repo name uses kebab-case, package uses snake_case (see rationale in [docs/setup-flutter-web-github-pages.md](../docs/setup-flutter-web-github-pages.md)). Keep `--base-href` aligned to repo name when forking/renaming.
- **Assets/fonts**: None configured beyond Material/Cupertino defaults. Add assets by updating `flutter/assets` section in `pubspec.yaml` and consider hot reload expectations.
- **Code style**: Prefer concise widget builds; avoid unnecessary comments. Keep ASCII in source unless localization requires otherwise.
- **When adding navigation**: Introduce routes explicitly in `MaterialApp` (e.g., `routes` map) and adjust 404 copy step if switching to multi-page routing with hashless URLs.
- **When changing deploy target**: If repo name or hosting path changes, update `--base-href` in workflow and any docs; ensure GitHub Pages source matches `gh-pages` output.
- **Troubleshooting**: Blank page on Pages usually means incorrect base href or Pages branch not configured after first Action run; rerun workflow and set Pages source.
