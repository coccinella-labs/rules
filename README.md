# Repository rules

Snapshots of the branch protection and merge-queue configuration for every
`coccinella-labs` repository that has a ruleset.

## These are independent records, not synchronized policies

Each JSON file is an independent export of one repository's ruleset at one
moment. Nothing in this repository applies them, and a change here does not
change any live repository.

This matters because the organization is on GitHub Free, which does not
provide the organization-level ruleset API. Every repository manages its own
ruleset independently, so there is no mechanism to push one canonical policy
to all of them. Automated synchronization would require upgrading the plan, or
another supported route.

Treat these files as a record of what each repository was configured to do, and
as the starting point for a review of whether those configurations should
diverge.

## Layout

```
rules/
├── README.md
├── CHANGELOG.md
└── repositories/
    ├── README.md          index and comparison table
    ├── manifest.json      provenance: source repo, ruleset id, export time
    ├── agentware.json
    ├── harper.json
    ├── mole.json
    └── organictyping.json
```

One snapshot per repository, named after it, rather than one file per policy
name. A policy-oriented layout was considered and rejected: the four live
rulesets share no common structure, so naming files after policies would imply
a shared taxonomy that does not exist.

## Each file is an importable payload

The snapshots contain only the fields the GitHub ruleset create and update API
accepts: `name`, `target`, `enforcement`, `conditions`, `rules`, and
`bypass_actors`.

Server-assigned and read-only fields are stripped, because they are not part of
the import contract: `id`, `node_id`, `created_at`, `updated_at`, `_links`,
`source`, `source_type`, `current_user_can_bypass`. Their values are preserved
in `manifest.json`.

The strip was verified lossless: for all four repositories, every rule, every
rule parameter, the conditions and the bypass actors are byte-identical before
and after stripping. Only the top-level server-assigned fields are removed.

Each file is valid JSON with no comment header, so it can be fed to the API
directly.

### Import compatibility is tested

All four payloads were imported into disposable repositories and read back.
Every one was accepted, and every one round-tripped identically: the ruleset the
API returned matched the snapshot field for field, and mole's and agentware's
matched their original live exports rule for rule.

```bash
gh api repos/OWNER/REPO/rulesets --method POST --input repositories/mole.json
gh api repos/OWNER/REPO/rulesets/RETURNED_ID
```

Ruleset names must be unique within a repository. Two snapshots named
`main-merge-queue`, `agentware` and `mole`, cannot be imported into the same
repository under their recorded names; one needs renaming at import time. This
is a constraint of the API, not a defect in either file.

## Keeping snapshots current

A snapshot is a record of what a ruleset looked like at one moment. It does not
keep itself current, and nothing in this repository watches the live
configuration.

When a live ruleset changes, refresh its snapshot and manifest in the same pull
request that records the change. The cheap drift check is `updated_at` in
`manifest.json`: if it is newer than the live `updated_at`, the snapshot is
stale.

```bash
gh api repos/coccinella-labs/agentware/rulesets --jq '.[].updated_at'
```

A snapshot refreshed without a corresponding live change is equally wrong: it
would claim a configuration that does not exist.

## Provenance

`repositories/manifest.json` records, for each snapshot:

| Field | Meaning |
| --- | --- |
| `repository` | Source repository |
| `file` | Snapshot path in this repository |
| `ruleset_id` | Ruleset id on GitHub at export time |
| `ruleset_name` | Ruleset name |
| `created_at` | When the ruleset was created upstream |
| `updated_at` | When it was last changed upstream |
| `exported_at` | When this snapshot was taken |
| `export_method` | The API call used |

## Working with these files

**Compare a snapshot against live configuration.** The `updated_at` in the
manifest is the cheap first check: if it is newer than the manifest entry, the
live ruleset has changed since the snapshot and the file is out of date.

```bash
gh api repos/coccinella-labs/agentware/rulesets \
  --jq '.[] | {id, name, updated_at}'
```

**Refresh a snapshot.** Export, strip, and confirm the strip is lossless before
committing. Do not hand-edit a snapshot to describe a change; a snapshot is a
record of what was live, so the live configuration changes first and the file
follows in a pull request.

**Propose a change.** Change the live ruleset through its API or the GitHub UI,
then refresh the snapshot here in the same pull request so the record and the
live state cannot drift apart.

## Not covered

Branch protection on repositories that have no ruleset, repository settings
that are not rulesets, organization-level configuration, and anything the
rulesets API does not expose. Absence of a file means no ruleset, not an
absence of protection.
