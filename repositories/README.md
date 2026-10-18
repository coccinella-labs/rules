# Repositories

One snapshot per repository that has a ruleset. See `manifest.json` for
provenance and the parent README for how these files are produced.

## Index

| Repository | Ruleset | Rules | Queue | Thread resolution | Approvals | Bypass |
| --- | --- | --- | --- | --- | --- | --- |
| [agentware](../README.md) | `main-merge-queue` | 3 | MERGE, ALLGREEN, 5m | required | 0 | Maintain, always |
| [mole](../README.md) | `main-merge-queue` | 7 | SQUASH, ALLGREEN, 5m | required | 0 | none |
| [harper](../README.md) | `Main Branch Protection` | 4 | none | not required | 1 | OrganizationAdmin, always |
| [organictyping](../README.md) | `main` | 3 | none | not required | 1 | none |

## Comparison

The four configurations differ substantially. They are not variants of one
policy and should not be read as interchangeable.

### Required status checks

| Repository | Checks |
| --- | --- |
| mole | `build`, `build-and-push`, `pre-commit`, `test (3.12)`, `test (3.13)`, `test (3.14)`, `e2e` |
| agentware | `build (ubuntu, debug)`, `build (windows, release)`, `lint` |
| harper | `CI (ubuntu-latest)` |
| organictyping | none |

Four different check-naming conventions across four repositories.

### Merge queue

| Repository | Method | Grouping | Wait |
| --- | --- | --- | --- |
| agentware | `MERGE` | `ALLGREEN` | 5 min |
| mole | `SQUASH` | `ALLGREEN` | 5 min |
| harper | none | | |
| organictyping | none | | |

The two repositories with a queue also disagree on merge method, so their
histories will look different: agentware keeps individual merge commits, mole
produces one squashed commit per pull request.

### Allowed merge methods

| Repository | Allowed |
| --- | --- |
| agentware | `merge`, `squash`, `rebase` |
| mole | `merge`, `squash`, `rebase` |
| harper | `squash`, `rebase` |
| organictyping | `merge`, `squash`, `rebase` |

harper disallows merge commits outright. Its `merge_method` in any future queue
configuration would be constrained accordingly.

### Bypass actors

| Repository | Actor | Mode |
| --- | --- | --- |
| agentware | RepositoryRole 5, the Maintain role | `always` |
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
| mole | no | no |

agentware and mole rely on the merge queue rather than explicit deletion and
force-push rules.

### Review requirements

| Repository | Thread resolution | Approvals |
| --- | --- | --- |
| agentware | required | 0 |
| mole | required | 0 |
| harper | not required | 1 |
| organictyping | not required | 1 |

## Observed differences, not a judgement

Recorded as observed. Whether each divergence is intentional has not been
established, and nothing here should be read as a recommendation either way.

The differences most worth confirming with a maintainer:

- **`organictyping` has no required status checks** while the other three do.
- **`harper` and `organictyping` do not require review thread resolution**, and
  both require one approving review where agentware and mole require none.
- **`mole` has no bypass actor**, so there is no path to merge without going
  through the queue.
- **`agentware` is the only repository where a bot may enqueue its own pull
  request** without a human approval, because its approval count is 0.

None of these were changed in creating this archive.
