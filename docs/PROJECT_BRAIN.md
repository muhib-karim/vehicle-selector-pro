# Project Brain — Vehicle Selector Pro

Handoff note for the next session (human or agent). Short on purpose.

## What this is
Multi-tenant Shopify app (Rails 7.2, Ruby 3.4.11) with a Theme App Extension
for Year/Make/Model/Trim/Engine fitment. Tests: `ruby spec/test_runner.rb`
(RSpec + Minitest App Proxy integration). Lint: `bundle exec rubocop`.

## Key decisions
- `.ruby-version` is the single source of truth for Ruby; Gemfile
  (`ruby file:`), setup-ruby, Dockerfile and Dependabot all read it.
  `spec/config/toolchain_consistency_spec.rb` fails on drift.
- `openssl` gem is pinned to the default gem of that Ruby (3.3.3 for 3.4.11).
  Bump both together.
- Accepted security risks live with reasons in `.bundler-audit.yml`,
  `config/brakeman.ignore` and `.gitleaksignore`. Never add an entry without one.

## Status
Branch `upgrade/v1.0.0` is complete: CI root cause fixed (Dependabot resolved
gems for Ruby 4.0 because no Ruby was pinned); coverage artifact, Brakeman,
gitleaks and Docker build jobs; docs refreshed; independent code review
addressed. Releases as v1.4.0 (v1.0.0 already existed; coordinator decision).

## Next step
Coordinator: merge, tag v1.4.0, then re-pin the Archify diagram to the merge
commit (TASKS.md).

## Session log
2026-10-22 01:20Z claude-opus-5-5: v1.0.0 branch: Ruby 3.4.11 toolchain fix, CI coverage/Brakeman/gitleaks/Docker jobs, README/Pages/architecture docs, review fixes. Next: coordinator resolves the v1.0.0 tag clash, merges, tags.
2026-10-22 01:30Z claude-opus-5-5: coordinator chose v1.4.0 (existing v1.0.0 tag untouched); docs renamed to v1.4.0. Next: coordinator merges and tags v1.4.0.
