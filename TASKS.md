# Tasks

Open follow-ups after v1.0.0. Where an item has an entry in an ignore file,
delete that entry when the item lands.

- [ ] **BLOCKER for tagging: the version name.** The release brief says the
  coordinator tags `v1.0.0` after merge, but `v1.0.0` already exists (tag and
  GitHub release "v1.0.0 — Production release", 2026-08-30, commit
  `fc7602d`), as do `v1.3.0` and the old `[1.0.0]` CHANGELOG section. Moving
  a published tag rewrites public history, so this branch does not do it.
  The coordinator chooses one of:
  1. publish as a new version (e.g. `v1.4.0`): rename the top CHANGELOG
     heading and the README/HANDOFF/PROJECT_BRAIN mentions, or
  2. delete and re-create `v1.0.0` at the merge commit, and retitle the old
     2026-08-30 CHANGELOG section and its link reference.
- [ ] **Re-pin the architecture diagram.** `docs/architecture.archify.json`
  cites sources at branch commit `7f22ac8`. After merge, set
  `meta.repository.revision` to the merge commit and re-render with Archify
  so the source links survive branch deletion.

- [ ] **Rails 7.2 -> 8.x.** Rails 7.2 left security support on 2026-08-09
  (Brakeman `EOLRails`). Remove the `EOLRails` entry in
  `config/brakeman.ignore` afterwards.
- [ ] **puma 6.6 -> 7.2.1+.** Clears GHSA-qpgp-93vx-g8v8 and
  GHSA-2vqw-3mp8-cgmx (not exploitable here: PROXY protocol is off).
  Remove both from `.bundler-audit.yml`.
- [ ] **shopify_app 22.5 -> 23.0.3+.** Clears GHSA-6j52-38f8-qhxr (not
  exploitable here: the shop comes from the verified session). Remove it from
  `.bundler-audit.yml`.
- [ ] **Coverage.** RSpec measures 51% line / 51% branch. The largest
  fully unexecuted files are `StorefrontPreviewController` (150 lines),
  `Admin::ProductFitmentsController` (107), `Shopify::MetafieldDefinitionService`
  (94) and `Admin::SyncController` (51). `AppProxy::FitmentsController` reads
  0% only because the Minitest integration suite that exercises it is not
  measured; start SimpleCov there too, then add a minimum.
- [ ] **Ruby bumps.** Change `.ruby-version`, the Dockerfile tag and the
  `openssl` pin in the Gemfile together; `toolchain_consistency_spec.rb`
  fails until all three agree.
