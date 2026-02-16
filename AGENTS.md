# Repository Guidelines

## Project Structure & Module Organization
- `src/` contains server code split by executable: `src/login`, `src/char`, `src/map`, `src/web`, plus shared code in `src/common` and utilities in `src/tool`.
- `db/` holds YAML/text game databases, with mode-specific overrides in `db/re/` and `db/pre-re/`.
- `npc/` contains scripts (`npc/re`, `npc/pre-re`, `npc/custom`, `npc/test`).
- `conf/` stores runtime configuration; use `conf/import-tmpl/` and `conf/msg_conf/import-tmpl/` for local overrides.
- `sql-files/` has schema/data SQL and `sql-files/upgrades/` migration scripts.
- `3rdparty/` contains vendored dependencies used by Make/CMake builds.

## Build, Test, and Development Commands
- Autotools build (Linux):
  - `./configure --enable-prere=no --enable-buildbot=yes`
  - `make server` (builds login/char/map/web + import folders)
  - `make tools` (builds utility binaries like `yaml2sql`, `mapcache`)
- CMake build:
  - `cmake -S . -B cbuild -G "Unix Makefiles"`
  - `cmake --build cbuild`
- Quick runtime checks:
  - `./login-server --run-once`
  - `./char-server --run-once`
  - `./map-server --run-once`
- NPC/DB validation flow used in CI:
  - `./tools/ci/npc.sh`
  - `make import && make tools && ./yaml2sql`
  - `./tools/ci/sql.sh && make map && ./map-server --run-once`

## Coding Style & Naming Conventions
- Follow `.editorconfig`: UTF-8, final newline, trim trailing whitespace.
- Use tabs for `*.cpp`, `*.hpp`, `*.c`, `*.h`, Makefiles, and NPC scripts.
- Use 4 spaces for YAML (`*.yml`, `*.yaml`).
- Match local naming patterns: snake_case filenames and paired headers/sources (example: `skill_factory_archer.hpp/.cpp`).

## Testing Guidelines
- No single unit-test framework is enforced; validation is build + runtime smoke checks.
- Test both modes when relevant: Pre-Renewal (`--enable-prere=yes`) and Renewal (`--enable-prere=no`).
- For DB/NPC changes, run the CI-equivalent NPC/SQL checks above before opening a PR.

## Commit & Pull Request Guidelines
- Prefer short imperative commit subjects; optional issue link suffix is common (example: `Fix skillratio for X (#9807)`).
- Keep commits scoped by subsystem (`src/map`, `db`, `npc`, etc.).
- PRs should follow `.github/PULL_REQUEST_TEMPLATE.md`:
  - `Addressed Issue(s)`
  - `Server Mode` (Pre-Renewal, Renewal, or Both)
  - Clear description of behavior change and validation steps.
- Use 'bd' for task tracking
- atomic commits only.
