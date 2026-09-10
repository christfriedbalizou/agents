---
name: github-workflow-creator
description: Create or revise secure, quality-gated GitHub Actions workflows, including trusted dependency-update auto-approval and auto-merge. Use when a project needs CI, code security scanning, repository automation, or release workflow design; do not use solely to publish a package.
---

# GitHub Workflow Creator

Create the smallest set of GitHub workflows that gives a repository dependable quality gates, proportionate security checks, and safe maintenance automation. This is a project-specific portfolio baseline derived from LedgerLink, Homelab, and Lincl; existing repository instructions and tooling remain authoritative.

## Discover before designing

- Read repository instructions, README/contribution guidance, existing workflows, dependency configuration, and the actual local build, format, lint, type-check, test, container, and release commands.
- Define the workflow's required check names and failure conditions before editing. Reuse documented commands rather than inventing CI-only equivalents.
- Select language/runtime versions, test matrices, service containers, packaging checks, path filters, and deployment validation only when the repository's supported environments or architecture make them meaningful.
- Do not add a publishing job just because the project builds a package. Publishing—especially PyPI—is an opt-in, separately authorized release concern.

## Default workflow shape

- Use a CI workflow for `pull_request` and protected-branch `push` events. Give it `contents: read`, a concurrency group scoped to workflow and ref/PR, and `cancel-in-progress: true` for superseded CI runs.
- Make the quality job run the project's locked install, formatter check, linter, type checker, unit tests, build, and dependency audit where those tools exist. Fail visibly; do not hide failures with `continue-on-error`.
- Add separate jobs for real compatibility or integration risks: supported runtime matrix, database/service integration, clean-install smoke tests, browser E2E, container build, or infrastructure validation. Use service health checks and non-production credentials. Do not create empty matrices or ceremonial jobs.
- If branch protection needs one stable required check across optional jobs, add a final gate that runs with `if: always()` and fails unless all applicable prerequisites succeeded. Treat intentionally skipped, path-filtered jobs deliberately rather than confusing them with failures.
- Use `workflow_dispatch` for useful manual validation. Use schedules for checks that must detect drift between commits, such as weekly CodeQL or daily secret scanning.

## Security baseline

- Set top-level permissions to `contents: read`; grant a job only the additional permission it needs. Keep tokens out of logs and use `persist-credentials: false` for checkouts that do not need to push.
- Pin every third-party action to a full commit SHA, with a version comment. Keep action updates managed by Dependabot or Renovate.
- Add CodeQL for supported languages when it provides meaningful analysis, with `security-events: write` only on that workflow. Add a repository secret scan on pull requests, protected-branch pushes, and a schedule. Scan container images for high/critical vulnerabilities and secrets when the project ships an image.
- Do not expose secrets to code from forks. Avoid `pull_request_target` unless its security model is explicitly reviewed. A privileged `workflow_run` job must never check out, build, or execute the untrusted pull-request revision.
- Treat release and deployment jobs as separate trust boundaries: run only after the tested protected-branch revision or a verified release tag, use concurrency to prevent races, and use an environment for any human approval that remains necessary.

## Labels and maintenance automation

- Add label syncing and path-based PR labeling only when labels are maintained as repository configuration. Label fork PRs only with a read-safe token and no write-capable secrets, or restrict write labeling to same-repository PRs.
- Prefer Dependabot or Renovate according to existing repository practice. Configure dependency updates explicitly, limit concurrent PRs, group only compatible low-risk updates, and never auto-merge major upgrades by default.
- Auto-approval and auto-merge are allowed only for narrowly identified, trusted bot PRs (for example Dependabot, Renovate, or a release-management app), after the CI workflow has succeeded for the exact PR head SHA and all required checks pass. Restrict this to patch/minor/pin/digest dependency updates unless the repository explicitly expands it.
- Use a GitHub App token with only `contents: write` and `pull-requests: write` in the merge/approval job. Confirm the PR author, open state, target branch, allowed labels or changed-file scope, and head SHA immediately before approving or merging. Use `gh pr merge --squash --match-head-commit` (or equivalent) to prevent a changed PR head from being merged.
- Make the auto-approval/merge workflow observe completed CI through `workflow_run`, and query GitHub metadata using the trusted workflow context. It must not execute PR code. Check whether the repository's branch-protection rules accept an App approval and allow that App to merge before enabling it.
- Never automatically approve or merge contributor PRs, security-sensitive changes, infrastructure/deployment changes, lockfile-only updates whose provenance is unclear, or PRs with failed, missing, cancelled, or skipped required checks.

Read [portfolio conventions](references/portfolio-conventions.md) when choosing among these patterns or adding auto-approval/merge logic.

## Verify and hand off

- Validate workflow YAML and repository-supported commands locally where possible. Review event triggers, permissions, action pins, concurrency, branch filters, matrix coverage, cache keys, and secret references.
- Check that required-check names match branch protection and that a bot cannot bypass required checks or review policy. Document required GitHub App secrets and permissions by name only, never their values.
- Report workflows added or changed, commands/checks covered, automation eligibility, any required repository settings, and deliberately excluded publishing or deployment behavior.
