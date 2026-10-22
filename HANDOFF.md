# Handoff: v1.4.0

Branch `upgrade/v1.0.0` (from `main` at `14ab2d3`; the branch name predates
the version decision). Not merged; no PR opened. The coordinator reviews,
merges and tags `v1.4.0`.

> **Version decision (resolved).** `v1.0.0` already existed as a tag and
> GitHub release (2026-08-30, `fc7602d`), alongside `v1.3.0` (**RUN**:
> `git ls-remote --tags origin`, `release list`). The brief had assumed only a
> `demo-media` tag. The coordinator decided to publish this release as
> **`v1.4.0`**, the next version after `v1.3.0`, and to leave every existing
> tag untouched.

Every statement below is marked **RUN** (a command was executed and its
output observed) or **INFERRED** (reasoned from code, docs or logs, not
executed). CI run ids are GitHub Actions runs on
`muhib-karim/vehicle-selector-pro`.

## Root cause of the red CI

| Claim | Basis |
|---|---|
| Run 39296245242 (Dependabot PR #23, 2026-10-19) failed in RuboCop, Dependency audit and Tests at `ruby/setup-ruby` because `bundle install` exited 5 | **RUN**: `run view 39296245242 --log-failed` |
| The failing line was `rbs-4.2.0 requires ruby version >= 3.3, which is incompatible with the current version, 3.2.11` | **RUN**: same log |
| Run 38477695083 (the 2026-10-13 Dependabot bundler PR) failed the same way, same `rbs-4.2.0` line in all three jobs | **RUN**: `run view 38477695083 --log-failed` |
| `main` itself was green (run 38331980696) | **RUN**: `run list` |
| Dependabot resolves against `.ruby-version`, or the Gemfile `ruby` directive, and otherwise falls back to its own Ruby (4.0.x) | **INFERRED**: read dependabot-core's `definition_ruby_version_patch.rb` and `Dockerfile.updater-core` |
| Lockfile platforms were not the cause: `x86_64-linux-gnu` already matched the runner. `x86_64-linux` was added anyway, as the generic platform | **RUN**: main's CI was green with the old platforms |

## Fix and verification

| Claim | Basis |
|---|---|
| New `toolchain_consistency_spec.rb` failed before the fix (5/5, then the openssl example on its own) and passes after | **RUN**: local Ruby 3.4.11 |
| A first version of the openssl example passed against the old lockfile (Bundler had activated 3.1.0), so it was rewritten to read Ruby's default-gem specs | **RUN** |
| Full suite on Ruby 3.4.11: 119 RSpec examples (118 at run 39700882758, before the Dockerfile example), 0 failures; 13 Minitest runs, 0 failures; line coverage 51.1%, branch 51.06% | **RUN**: locally and in CI |
| RuboCop clean, zeitwerk check green | **RUN**: locally and in CI |
| bundler-audit: no unignored advisories. An empty ignore list reports only the puma and shopify_app advisories, so the seven Active Storage ignores were removed | **RUN**: local, advisory DB updated 2026-10-21 |
| Brakeman 8.1.0: 0 warnings after ignoring 2, each with a reason in `config/brakeman.ignore` | **RUN** |
| Rails 7.2 security support ended 2026-08-09 | **INFERRED**: Brakeman's EOLRails check |
| gitleaks 8.21.2 and 8.28.0: no leaks across the history and the working tree once 4 historical dummy-secret fingerprints are pinned | **RUN** locally (141 commits) and in the CI secret-scan job on every head below |
| The 4 pinned hits are the dummy value `...test_api_secret_...`, not a real credential | **RUN**: inspected the redacted matches and their commits |
| As first committed, the Dockerfile would not build: the gem layer copied only `Gemfile Gemfile.lock`, so `ruby file: ".ruby-version"` failed | **INFERRED** by the independent code review; fixed by copying `.ruby-version` first, and a spec example now fails if that copy goes missing (**RUN**: watched it fail, then pass) |
| Docker image builds on `ruby:3.4.11-slim` | **RUN in CI**: `Docker image build` job in run 39702785262 (no local Docker build, per box rule) |
| A Postgres service is not needed in CI | **INFERRED**: `config/database.yml` uses SQLite for test; Postgres is production-only |
| Coverage artifact `coverage-report` is uploaded | **RUN**: listed under ARTIFACTS of run 39700882758 |
| Dependabot already covers bundler and github-actions weekly | **RUN**: read `.github/dependabot.yml`; unchanged |
| The architecture diagram passed Archify's validate, deliver, check and browser-check gates and was reviewed visually | **RUN** |
| The Pages landing page renders in light, dark and at 390 px with no horizontal overflow; Impeccable detector reports no findings | **RUN**: headless Chromium screenshots |
| The new Pages links (`architecture.html`) resolve | **INFERRED**: only after merge, when `pages.yml` deploys from `main` |

## CI runs on this branch

Runs on this branch. The final head only renames the release to v1.4.0 in docs; its run id is in the coordinator's report.

| Run | Head | Result |
|---|---|---|
| 39700882758 | `7f22ac8` (toolchain fix, coverage, Brakeman, gitleaks) | success, all 5 jobs |
| 39702785262 | `6606961` (review fixes: Dockerfile, Docker build job, pinned scanners) | success, all 6 jobs including Docker image build; coverage-report artifact |

## Independent review

A separate reviewer read the branch diff (`14ab2d3..6956dec`). Its verdict was "with fixes":

- Critical: Dockerfile missing `.ruby-version` (fixed); existing `v1.0.0` tag (escalated; resolved by the coordinator as `v1.4.0`).
- Important: Dependabot would bump the `openssl` pin (now ignored); Brakeman unpinned (pinned to 8.1.0, bundler-audit to 0.9.3); docs commits had no CI run (see the table).
- Minor, fixed: gitleaks checksum, concurrency group, read-only permissions, `if-no-files-found: warn`, README `#demo` anchor kept, version-manager wording, platform spec accepts `x86_64-linux` or `x86_64-linux-gnu`, PROJECT_BRAIN status, TASKS wording.
- Minor, deferred: re-pin the diagram's source links after merge (TASKS.md).

## Follow-ups

See [TASKS.md](TASKS.md): Rails 8, puma 7, shopify_app 23, coverage, diagram re-pin.
