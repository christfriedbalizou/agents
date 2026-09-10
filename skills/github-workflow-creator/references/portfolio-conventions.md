# Portfolio workflow conventions

This reference records the reusable patterns observed in the three source repositories. It is a selection guide, not a mandate to reproduce every workflow.

## Quality and delivery

- **LedgerLink** uses a Node CI workflow with format, lint, type checking, unit tests, build, and dependency audit; separate jobs cover an explicit newer Node runtime, SQLite/PostgreSQL integration, Playwright E2E, and a container vulnerability/secret scan. A final `if: always()` gate makes all intended jobs visible as one required CI result.
- **Lincl** runs pre-commit and `pip-audit` from hash-locked tool requirements, tests each supported Python version, verifies clean source installation on supported Linux distributions, and verifies wheel/sdist installation and metadata. Its PyPI release workflow is intentionally *not* part of this skill's default.
- **Homelab** uses changed-file filtering so expensive Flux validation and PR diffs only run when Kubernetes files change, then exposes a final success job that tolerates deliberately skipped jobs while failing real failures.

Choose an integration or compatibility job only when it tests an actual supported contract. A clean installation test is valuable for a CLI/package; an image scan is valuable for a shipped image; Flux diff/test is valuable for a GitOps repository.

## Security and action integrity

LedgerLink and Lincl pin GitHub Actions to immutable full commit SHAs and annotate the human-readable action version; Homelab does the same for its JavaScript actions. Reproduce that practice for every external action, and use an immutable digest rather than a mutable tag when invoking a container action. They use a read-only workflow default and elevate permissions on just the jobs that synchronize labels, comment on PRs, or manage releases.

LedgerLink schedules TruffleHog secret scanning in addition to PR and main-branch scans. Lincl schedules CodeQL and limits its write permission to security-event upload. These are complementary: select CodeQL for supported source languages and a secret scanner for any repository that could accidentally receive credentials.

## Dependency and release automation

- LedgerLink's Renovate rules auto-merge only minor, patch, pin, and digest updates, while explicitly disabling major-version auto-merge.
- Lincl's Dependabot auto-merge workflow runs after CI, creates a narrowly permissioned GitHub App token, finds an open PR whose author is `dependabot[bot]` from the tested head SHA, waits for checks, and squash-merges it. Its Release Please merge workflow additionally identifies the expected release label and verifies the head commit before merge.
- Homelab runs Renovate with a GitHub App token and schedules it regularly. Its labeler protects write access by running only for same-repository PRs.

For a separate auto-approval step, preserve the same trust checks before submitting the review. Ensure the approver is an App or identity permitted by branch protection and is not the PR author. Approval alone does not make a PR eligible to merge: CI and required reviews must still be satisfied.

## Required repository setup

When bot automation is selected, document these prerequisites in the repository rather than assuming them:

- GitHub App ID/client ID and private-key secrets, with the App installed on the repository.
- Least-privilege App permissions for the exact operation (normally pull requests and contents write for approval/merge).
- Branch-protection or ruleset settings that require the CI gate and allow the App to approve/merge only where intended.
- Dependabot/Renovate configuration that makes eligible update type and source explicit.

Do not grant blanket workflow write permissions, use a personal access token, or make arbitrary PRs eligible merely because a workflow completed successfully.
