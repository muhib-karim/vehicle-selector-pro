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
v1.0.0 upgrade branch `upgrade/v1.0.0`: CI root cause fixed (Dependabot
resolved gems for Ruby 4.0 because no Ruby was pinned), coverage artifact,
Brakeman and gitleaks jobs added. Docs refresh in progress.

## Next step
Finish README/CHANGELOG/SKILLS_USED/HANDOFF, push via the gated push script,
get CI green. After merge, the coordinator tags v1.0.0.

## Session log
