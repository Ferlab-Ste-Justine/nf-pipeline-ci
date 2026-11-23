# nf-pipeline-ci

Reusable GitHub Actions workflows (`workflow_call`) and composite actions shared by the Ferlab Nextflow pipelines:

- [snv-post-processing](https://github.com/Ferlab-Ste-Justine/snv-post-processing)
- [cnv-post-processing](https://github.com/Ferlab-Ste-Justine/cnv-post-processing)
- [quality-control-pipeline](https://github.com/Ferlab-Ste-Justine/quality-control-pipeline)

This repo holds only CI plumbing. Tests (`*.nf.test`, snapshots, fixtures), `nf-test.config` and
`conf/test*.config` stay in each pipeline repo.

## Contents

| Path | Kind | Purpose |
| --- | --- | --- |
| `.github/workflows/actionlint.yml` | Internal | Lints this repo's workflows and actions on every PR. Required check on `main`. |

Shared workflows and actions are added here as they are migrated out of the pipelines
(`copy-test-data` → `linting.yml` → `nf-test.yml`). Each one documents its inputs in the table
below once it exists.

## Inputs

No reusable workflow or composite action is published yet.

<!--
Template for each shared piece:

### `.github/workflows/<name>.yml` (reusable workflow)

| Input | Type | Required | Default | Description |
| --- | --- | --- | --- | --- |

Secrets: list them, or "none". Callers pass secrets with `secrets: inherit`.
-->

## Using a shared workflow

Callers pin by full commit SHA, with the release tag in a comment, so Dependabot can open bump PRs:

```yaml
jobs:
  nf-test:
    uses: Ferlab-Ste-Justine/nf-pipeline-ci/.github/workflows/nf-test.yml@<sha> # v1.2.0
    with:
      test_data_s3_path: s3://ferlab-public-dataset/nextflow/<pipeline>/<version>/data-test
    secrets: inherit
```

The caller repo needs `package-ecosystem: github-actions` in its `dependabot.yml`.

## Versioning

- Releases are SemVer tags (`vX.Y.Z`) on `main`.
- **Major**: an input removed or renamed, a default changed in a way that alters results, or a new
  required secret. **Minor**: a new optional input or a new shared piece. **Patch**: fixes only.
- Never move or delete a published tag; callers pin its SHA.
- Release notes list every input change and what callers must do.

## Contributing

- Open a PR against `main`. One approval from `@Ferlab-Ste-Justine/bioinfo-devs-1` is required.
- Branch protection, required checks, secrets and org Actions policy are managed by DevOps.
- Rules for workflows that are, or may become, required checks:
  - Never rename a required job. GitHub keeps waiting for the old name and blocks every PR.
  - No `paths:` filter. A skipped workflow never reports, so the PR stays blocked.
  - For matrix jobs, require one aggregator job, not each combination.
- Pin every third-party action by full commit SHA, with its tag in a comment.
- The repo must stay public: public pipelines cannot call reusable workflows in a private repo.
