# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Structure

This repo is a thin wrapper: the actual Flutter application lives entirely under `flutter_project_template/`, not at the repo root. Always `cd flutter_project_template` before running Flutter/Dart commands.

```
flutter_project_template/   # the Flutter app (package name: gsexampleapp)
  lib/main.dart              # entire app entry point — currently a minimal scaffold
  test/widget_test.dart      # stock flutter-create counter test (stale, see Known Issues)
  assets/images/              # app icon source for flutter_launcher_icons
  android/ ios/ web/ macos/ windows/ linux/  # standard Flutter platform folders
  Makefile                    # all dev/build/deploy commands
  pubspec.yaml
```

This is package `genericsuite-mobile-exampleapp` inside the larger `genericsuite` superproject (see `../../CLAUDE.md` for monorepo-wide context, packages, and cross-package architecture). It demonstrates/exercises the `genericsuite` Flutter library from the sibling package `genericsuite-mobile`.

## Critical Dependency: sibling package via relative path

`pubspec.yaml` pulls the `genericsuite` package as a **git dependency pointing at a relative local path**:

```yaml
genericsuite:
  git:
    url: ../../genericsuite-mobile
    ref: develop
    path: genericsuite_flutter
```

This only resolves correctly when this repo is checked out as a sibling of `genericsuite-mobile` inside the `genericsuite` superproject's `packages/` directory (i.e. `packages/genericsuite-mobile-exampleapp` and `packages/genericsuite-mobile` both present). Running `flutter pub get` outside that layout will fail to resolve the dependency. See `packages/genericsuite-mobile/CLAUDE.md` for the library's architecture (JSON-driven CRUD, `AppCallablesSuper`, `CreateGsApp`, GetIt DI, auth flow) — that's where most of the actual behavior this app exercises is implemented.

## Commands

All commands below run from `flutter_project_template/`.

```bash
make install        # flutter pub get
make update          # flutter pub upgrade
make test            # flutter test (aliased as `make qa`)
flutter analyze      # lint/static analysis (not wrapped in Makefile)

make run             # = run_local: config_local + clean_logs + flutter run
make run_web         # = run_web_local: flutter run -d chrome

make build_local      # flutter build apk --debug
make build            # flutter build apk --release
make build_bundle     # release .aab + zipped debug symbols, for Play Store

make clean            # pub cache clean + rm build/ + rm .dart_tool + rm logs/ + flutter clean
make fresh            # clean + install

make generate_icons   # regenerate launcher icons from assets/images/app_logo_circle.png
```

Run a single test file directly with `flutter test test/widget_test.dart` (no Makefile target for single-file runs).

### Config/deploy targets are stubs

`config`, `config_dev`, `config_qa`, `config_staging`, `config_qa_for_deployment`, and `link_config_dirs` in the Makefile are placeholders — their bodies are commented out (`@echo "Uncomment this Makefile block..."`). They exist to be filled in when this app is wired into a specific monorepo deployment (copying `assets/config_dbdef`, images, and swapping `assets/config/stage.json`), following the same pattern used by other GenericSuite frontend projects via `genericsuite-fe-scripts`. As shipped, `run_local`/`run_qa`/`deploy_web_qa` etc. call these no-op config steps.

## Current State / Known Issues

- `lib/main.dart` is a minimal placeholder: it imports `genericsuite`'s `create_gs_app.dart` and `theme_config_defaults.dart` but only uses `buildGsMaterialTheme()` for theming — it does not yet call `CreateGsApp`, mount `CrudEditor`, or wire up `AppCallablesSuper`/menus/auth as described in the `genericsuite-mobile` library docs. `AppHome` just pushes copies of itself via a button.
- `test/widget_test.dart` is the unmodified `flutter create` counter-app smoke test (expects `find.text('0')`/`'1'` and a `+` icon) — it does not match the current `main.dart` and will fail if run as-is. Update or replace it before relying on `make test`.

## Non-Negotiable Rules (inherited from superproject)

- Shell: `bash` only (macOS/Linux; WSL on Windows).
- Package manager: Flutter/`pub` — do not introduce other tooling.
- Secrets: never hardcode; use stage-specific config under `assets/config*` per the `genericsuite-mobile` pattern.
- `flutter analyze` should pass before merging (inherited from `genericsuite-mobile`'s lint policy; this repo uses the same `flutter_lints` base in `analysis_options.yaml` with no added rules).
