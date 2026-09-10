# GitHub workflow patterns

This reference gives reusable workflow patterns. It is a selection guide, not a mandate to reproduce every workflow.

## Quality and delivery

- Run format, lint, type checking, unit tests, build, and dependency audit where the repository provides those checks.
- Add separate jobs for supported runtime compatibility, database or service integration, clean installation, browser E2E, container build, or infrastructure validation only when they test an actual supported contract.
- Use changed-file filtering for expensive checks when a path cannot affect their result. Expose a final success job that deliberately accepts those skipped jobs while failing real failures.
- Keep package publishing separate from ordinary CI. Add a release workflow only when publishing is explicitly authorized.

A clean-installation test is valuable for a CLI or package; an image scan is valuable for a shipped image; infrastructure diff or validation is valuable for an infrastructure repository.

## Security and action integrity

Pin every external GitHub Action to an immutable full commit SHA and annotate its human-readable version. Use an immutable digest rather than a mutable tag when invoking a container action. Default workflows to read-only permissions and elevate permissions only for jobs that synchronize labels, comment on pull requests, or manage releases.

Run a secret scan on pull requests, protected-branch pushes, and a schedule. Use CodeQL for supported languages and limit its write permission to security-event upload. These checks are complementary: select the checks that address the repository's credible risks.

## Dependency and release automation

- Auto-merge only minor, patch, pin, and digest dependency updates by default; explicitly exclude major-version updates.
- Run auto-merge only after CI for the exact tested head SHA. Use a narrowly permissioned GitHub App token, verify the trusted bot author, PR state, target branch, eligibility rules, required checks, and head commit, then squash-merge with a head-SHA guard.
- For separate auto-approval, preserve the same trust checks before submitting the review. Ensure the approver is an App or identity permitted by branch protection and is not the PR author. Approval alone does not make a PR eligible to merge: CI and required reviews must still be satisfied.
- Label fork pull requests only with a read-safe token and no write-capable secrets, or run write labeling only for same-repository pull requests.

## Required repository setup

When bot automation is selected, document these prerequisites in the repository rather than assuming them:

- GitHub App ID/client ID and private-key secrets, with the App installed on the repository.
- Least-privilege App permissions for the exact operation (normally pull requests and contents write for approval/merge).
- Branch-protection or ruleset settings that require the CI gate and allow the App to approve/merge only where intended.
- Dependabot/Renovate configuration that makes eligible update type and source explicit.

Do not grant blanket workflow write permissions, use a personal access token, or make arbitrary PRs eligible merely because a workflow completed successfully.
