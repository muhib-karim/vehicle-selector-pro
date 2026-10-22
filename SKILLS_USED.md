# Skills used

Which agent skill drove which change. Newest release first.

## v1.0.0

| Skill | What it drove |
|---|---|
| systematic-debugging | Phase 1 on the red Dependabot run: read the failed log (`rbs-4.2.0 requires ruby >= 3.3`), compared with green `main`, then traced how Dependabot picks a Ruby (dependabot-core's ruby-version patch) before changing anything. Root cause: no `.ruby-version`, open-ended Gemfile `ruby` |
| brainstorming | Scope check. The frozen task already fixed scope, so it ended with one stated call: Ruby 3.4.11 in a `.ruby-version` every consumer reads |
| writing-plans | `docs/superpowers/plans/2026-10-22-v1-0-0-upgrade.md` (three tasks, review-focus list) |
| test-driven-development | `spec/config/toolchain_consistency_spec.rb` written first and watched failing. Its "watch it fail" step caught a tautological openssl assertion, which was rewritten |
| ponytail (repo `AGENTS.md`) | Smallest lockfile diff (platform, Bundler, openssl only; no broad `bundle update`), gitleaks CLI instead of a licensed action, reuse of existing demo media |
| security-and-hardening | Brakeman and gitleaks jobs; every accepted risk gets a reason (`config/brakeman.ignore`, `.gitleaksignore`, `.bundler-audit.yml`); stale ignores removed |
| ci-cd-and-automation | Coverage artifact (`if: always()`), CI on `upgrade/**`/`release/**`, setup-ruby reading `.ruby-version` |
| archify | Code-backed architecture diagram: `docs/architecture.archify.json` -> interactive `report/site/architecture.html` and the README PNG. Every node cites source lines at a pinned commit |
| impeccable | Polish pass on the Pages landing page: removed the eyebrow kicker, AA-contrast accent, dark mode, `:focus-visible`, selection colour, reduced motion; detector run clean |
| ui-ux-pro-max | UX guideline lookups (focus states, contrast) behind the same page; 44 px button height |
| verification-before-completion | Nothing claimed without output: local runs, CI run ids and RUN/INFERRED labels in `HANDOFF.md` |
| requesting-code-review | One independent reviewer on the full branch diff. It caught the Dockerfile `.ruby-version` copy bug and the existing `v1.0.0` tag; fixes are listed in `HANDOFF.md` |

Not used, and why: understand / graphify (heavy whole-repo graph builds on a memory-tight machine; archify's source-cited diagram covered the architecture section), playwright (headless Chromium screenshots were enough for one static page), subagent-driven-development (the machine forbids parallel subagents).
