# Changelog

All notable changes to the ruleset snapshots are recorded here.

The format follows [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

Entries are dated. Snapshots are refreshed when a live ruleset changes, and
those refreshes are the changes worth recording here. A change to a live
ruleset without a matching snapshot refresh is a defect in this repository.

## [Unreleased]

### Added

- Added `repositories/ellis.json` for `coccinella-labs/ellis`, snapshot of ruleset `main-protection` (id 26288517) exported 2026-10-31:
  - Required status checks: `Code Quality: Push on main`, `CodeQL`, `Docker`, `E2E Tests`, `Release`, `Validate Workflows`, `CLI`.
  - 1 required approving review, review thread resolution required, signatures required.
  - `Maintain` role bypass enabled (`always`).
  - Updated `manifest.json` provenance for the new entry.

### Changed

- Refreshed `repositories/ellis.json` to match the live API state as of 2026-11-02:
  - `updated_at` advanced from 2026-10-31 to 2026-11-02 (the ruleset was toggled
    through branch protection during a force-push cycle and restored to its
    original configuration).
  - Verified the snapshot is still byte-identical to the live ruleset: all
    rules, rule parameters, conditions, enforcement, and bypass actors match.
  - Updated `manifest.json` provenance (`updated_at` and `exported_at`) for the
    entry.

### Added

- Added `repositories/ellis-main-merge-queue.json` for `coccinella-labs/ellis`,
  snapshot of ruleset `main-merge-queue` (id 26353638) exported 2026-11-02. This
  ruleset was missing from the archive entirely:
  - Required status checks: `Ellis`, `Validate Workflows`, `Docker`, `E2E Tests`,
    `CLI`.
  - Merge queue: `SQUASH`, `ALLGREEN`, 5 min wait, 5 max entries to build.
  - `OrganizationAdmin` bypass enabled (`always`) in addition to the `Maintain`
    role bypass that `main-protection` also grants.
  - Updated `manifest.json` provenance for the new entry.
  - Updated the comparison index in `repositories/README.md` to cover both
    ellis rulesets.

## [0.1.0] - 2026-10-19

### Added

- Initial archive of the four live rulesets in the organization: `agentware`,
  `mole`, `harper` and `organictyping`.
- `repositories/manifest.json` recording provenance for each snapshot: source
  repository, upstream ruleset id, upstream created and updated timestamps,
  export time, and the API call used.
- A comparison index in `repositories/README.md` covering required checks, merge
  queue settings, allowed merge methods, bypass actors, branch-level rules and
  review requirements, so the snapshots can be reviewed without being treated as
  interchangeable.
- Import compatibility verified for all four payloads. Each was imported into a
  disposable repository and read back; all four were accepted and all four
  round-tripped identically. The disposable repositories have been deleted.

### Notes

- Snapshots are independent records, not synchronized policies. The
  organization is on GitHub Free, which does not provide the organization-level
  ruleset API, so no mechanism exists to apply one policy across repositories.
- Differences between the four configurations are recorded as observed. Whether
  each is intentional has not been established and no live ruleset was modified
  to create this archive.