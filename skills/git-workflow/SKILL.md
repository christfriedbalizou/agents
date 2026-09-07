---
name: git-workflow
description: Prepare focused commits and review-ready pull requests while preserving repository history and user changes. Use for commits, branches, pull requests, or merge readiness.
---

# Git workflow

Read the repository's contribution, branch, and release rules first; they override this skill. Inspect the current branch and working tree before switching branches, staging files, or committing. Preserve unrelated changes.

## Commit discipline

- Make each commit one coherent, reviewable change; include its tests, migrations, generated artefacts, and documentation only when the repository intentionally tracks them.
- Prefer Conventional Commit types when no local convention exists: `feat`, `fix`, `refactor`, `test`, `docs`, `perf`, `build`, `ci`, and `chore`.
- Write an imperative, specific subject that describes the observable change.
- Review the staged diff and repository status before committing. Check for secrets, generated runtime data, debug output, unrelated edits, and unsafe migration changes.

## Publishing and merge readiness

Publish only with explicit authorization or a repository policy that grants it. Before opening or merging a pull request, ensure the change is based on the intended target, relevant local checks pass, and the description records scope, verification, risks, and migration or rollback considerations where relevant.

CI completion is evidence, not merge authority. Never merge with unresolved review findings, failing or incomplete required checks, unreviewed destructive migrations, known secret exposure, or uncertainty about the validated commit.
