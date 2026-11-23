# CLAUDE.md

Shared CI for the Ferlab Nextflow pipelines (snv-post-processing, cnv-post-processing,
quality-control-pipeline): reusable workflows (`on: workflow_call`) and composite actions only.
Tests, snapshots, fixtures, `nf-test.config` and `conf/test*.config` stay in each pipeline repo.

Roadmap and task list: "Pipeline fleet testing roadmap" doc,
https://claude.ai/artifact/WuYXHiyjSVzpx4S3pKDTLU

## Commits and branches

- Commit title format: `<type>: OPSSUP-148 <summary>`, imperative, lowercase after the ticket.
  Example: `git commit -m "chore: OPSSUP-148 add actionlint workflow and README"`
- `<type>` is a Conventional Commits type: `chore`, `ci`, `feat`, `fix`, `docs`, `refactor`, `test`.
- The ticket is OPSSUP-148 unless the user gives another one.
- Never commit to `main`; branch first, open a PR. One PR per task.

## Rules for workflows and actions

- Pin every third-party action by full commit SHA with its tag in a comment:
  `uses: actions/checkout@<sha> # v7.0.1`. Get the SHA with
  `git ls-remote https://github.com/<owner>/<repo> 'refs/tags/<tag>*'` (use the `^{}` line if present).
- Binaries downloaded in a workflow are pinned by version and verified with `sha256sum --check`.
- Default to `permissions: contents: read` and `persist-credentials: false` on checkout.
- Required checks (`actionlint`, and the self-test aggregator once it exists):
  - never rename a required job: GitHub waits for the old name and blocks every PR;
  - no `paths:` filter on a required workflow: a skipped workflow never reports;
  - for matrices, require one aggregator job, not each combination.
- The repo must stay public: public pipelines cannot call reusable workflows in a private repo.
- Versioning is SemVer tags on `main`; never move a published tag. See README "Versioning".
- Document every input and secret of a shared piece in the README "Inputs" section.

## Checking changes

Lint locally before committing (same pinned version as CI):

```bash
actionlint -color   # from the repo root; install shellcheck too so run: blocks are checked
```

## Ownership

- Bioinfo (`@Ferlab-Ste-Justine/bioinfo-devs-1`, maintain): workflows, actions, CODEOWNERS,
  dependabot.yml, tests. Changes go through PR.
- DevOps (admin): branch protection, required status checks, org Actions policy, teams, secrets.
  Changes there are requests to DevOps, not something to do from this repo.

## Environment

- `gh` is not installed in the devcontainer; GitHub API calls must be run by the user on the host.
