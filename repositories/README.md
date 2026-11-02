# Repositories

One snapshot per repository that has a ruleset. See `manifest.json` for
provenance and the parent README for how these files are produced.

## Index

| Repository | Ruleset | Rules | Queue | Thread resolution | Approvals | Bypass |
| --- | --- | --- | --- | --- | --- | --- |
| [agentware](../README.md) | `main-merge-queue` | 3 | MERGE, ALLGREEN, 5m | required | 0 | Maintain, always |
| [ellis](../README.md) | `main-protection` | 3 | none | required | 1 | Maintain, always |
| [ellis-merge-queue](../README.md) | `main-merge-queue` | 2 | SQUASH, ALLGREEN, 5m | n/a | n/a | OrganizationAdmin, always |
| [harper](../README.md) | `main-protection` | 4 | none | not required | 1 | OrganizationAdmin, always |
| [harper-merge-queue](../README.md) | `main-merge-queue` | 2 | SQUASH, ALLGREEN, 5m | n/a | n/a | none |
| [mole](../README.md) | `main-merge-queue` | 7 | SQUASH, ALLGREEN, 5m | required | 0 | none |
| [organictyping](../README.md) | `main` | 3 | none | not required | 1 | none |

## Comparison

The configurations differ substantially. They are not variants of one
policy and should not be read as interchangeable.

### Required status checks

| Repository | Checks |
| --- | --- |
| mole | `build`, `build-and-push`, `pre-commit`, `test (3.12)`, `test (3.13)`, `test (3.14)`, `e2e` |
| agentware | `build (ubuntu, debug)`, `build (windows, release)`, `lint` |
| ellis | `Code Quality: Push on main`, `CodeQL`, `Docker`, `E2E Tests`, `Release`, `Validate Workflows`, `CLI` |
| ellis (merge queue) | `Ellis`, `Validate Workflows`, `Docker`, `E2E Tests`, `CLI` |
| harper | `CI (ubuntu-latest)` |
| organictyping | none |

Six different check-naming conventions across six repositories.

### Merge queue

| Repository | Method | Grouping | Wait |
| --- | --- | --- | --- |
| agentware | `MERGE` | `ALLGREEN` | 5 min |
| ellis (merge queue) | `SQUASH` | `ALLGREEN` | 5 min |
| mole | `SQUASH` | `ALLGREEN` | 5 min |
| harper | none | | |
| harper (merge queue) | `SQUASH` | `ALLGREEN` | 5 min |
| organictyping | none | | |

Four repositories have a queue. ellis and mole use `SQUASH`, agentware
uses `MERGE`, and harper (merge queue) uses `SQUASH`. Their histories will
look different: agentware keeps individual merge commits, while mole and
ellis produce one squashed commit per pull request.

### Allowed merge methods

| Repository | Allowed |
| --- | --- |
| agentware | `merge`, `squash`, `rebase` |
| ellis | `merge`, `squash`, `rebase` |
| mole | `merge`, `squash`, `rebase` |
| harper | `squash`, `rebase` |
| organictyping | `merge`, `squash`, `rebase` |

harper disallows merge commits outright. Its merge queue uses `SQUASH`,
consistent with its `squash` and `rebase` allowed merge methods.

### Bypass actors

| Repository | Actor | Mode |
| --- | --- | --- |
| agentware | RepositoryRole 5, the Maintain role | `always` |
| ellis | RepositoryRole 5, the Maintain role | `always` |
| harper | OrganizationAdmin | `always` |
| mole | none | |
| organictyping | none | |

`always` means the bypass is unconditional, including over required status
checks. mole and organictyping have no bypass path at all.

### Branch-level rules

| Repository | `deletion` | `non_fast_forward` |
| --- | --- | --- |
| harper | yes | yes |
| organictyping | yes | yes |
| agentware | no | no |
| ellis | no | no |
| mole | no | no |

agentware and mole rely on the merge queue rather than explicit deletion and
force-push rules.

### Review requirements

| Repository | Thread resolution | Approvals |
| --- | --- | --- |
| agentware | required | 0 |
| ellis | required | 1 |
| mole | required | 0 |
| harper | not required | 1 |
| harper (merge queue) | n/a (no PR rule) | n/a (no PR rule) |
| organictyping | not required | 1 |

## Observed differences, not a judgement

Recorded as observed. Whether each divergence is intentional has not been
established, and nothing here should be read as a recommendation either way.

The differences most worth confirming with a maintainer:

- **`organictyping` has no required status checks** while the other four do.
- **`harper` and `organictyping` do not require review thread resolution**, and
  both require one approving review where agentware, ellis, and mole require
  none or have different requirements.
- **`mole` has no bypass actor**, so there is no path to merge without going
  through the queue.
- **`agentware` is the only repository where a bot may enqueue its own pull
  request** without a human approval, because its approval count is 0.
- **`ellis` is the only repository that enforces required signatures** as a
  separate rule.

These were recorded as observed. The harper `main-merge-queue` ruleset
snapshot was added to track the merge queue configuration enabled after
the original archive was created.