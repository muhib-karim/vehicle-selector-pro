# Handoff: v1.0.0

Branch `upgrade/v1.0.0` (from `main` at `14ab2d3`). Not merged; no PR opened.
The coordinator reviews, merges and tags `v1.0.0`.

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
| Full suite on Ruby 3.4.11: 118 RSpec examples, 0 failures; 13 Minitest runs, 0 failures; line coverage 51.1%, branch 51.06% | **RUN**: locally and in CI run 39700882758 |
| RuboCop clean, zeitwerk check green | **RUN**: locally and in CI |
| bundler-audit: no unignored advisories. An empty ignore list reports only the puma and shopify_app advisories, so the seven Active Storage ignores were removed | **RUN**: local, advisory DB updated 2026-10-21 |
| Brakeman 8.1.0: 0 warnings after ignoring 2, each with a reason in `config/brakeman.ignore` | **RUN** |
| Rails 7.2 security support ended 2026-08-09 | **INFERRED**: Brakeman's EOLRails check |
| gitleaks 8.21.2 and 8.28.0: no leaks across 141 commits and the working tree once 4 historical dummy-secret fingerprints are pinned | **RUN** |
| The 4 pinned hits are the dummy value `...test_api_secret_...`, not a real credential | **RUN**: inspected the redacted matches and their commits |
| Docker image builds on `ruby:3.4.11-slim` | **INFERRED**: no local Docker build (box rule); no CI job builds the image. The openssl pin matches the default gem, so nothing needs OpenSSL headers |
| A Postgres service is not needed in CI | **INFERRED**: `config/database.yml` uses SQLite for test; Postgres is production-only |
| Coverage artifact `coverage-report` is uploaded | **RUN**: listed under ARTIFACTS of run 39700882758 |
| Dependabot already covers bundler and github-actions weekly | **RUN**: read `.github/dependabot.yml`; unchanged |
| The architecture diagram passed Archify's validate, deliver, check and browser-check gates and was reviewed visually | **RUN** |
| The Pages landing page renders in light, dark and at 390 px with no horizontal overflow; Impeccable detector reports no findings | **RUN**: headless Chromium screenshots |
| The new Pages links (`architecture.html`) resolve | **INFERRED**: only after merge, when `pages.yml` deploys from `main` |

## CI runs on this branch

The final head's run id is recorded in the coordinator's report; earlier runs:

| Run | Head | Result |
|---|---|---|
| 39700882758 | `7f22ac8` (toolchain fix, coverage, Brakeman, gitleaks) | success, all 5 jobs |

## Follow-ups

See [TASKS.md](TASKS.md): Rails 8, puma 7, shopify_app 23, coverage.
