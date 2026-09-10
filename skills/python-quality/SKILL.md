---
name: python-quality
description: Apply this opinionated Python production baseline for clear, typed, maintainable implementation and review.
---

# Python quality

This is a prescriptive production baseline. Apply its tooling and style rules consistently when a project adopts it.

## Implementation

- Prioritise correctness and data safety, then clarity and simplicity; optimise only when a measured requirement justifies the added complexity.
- Model expected failure paths explicitly rather than returning ambiguous sentinel values.
- Do not log secrets or sensitive data.
- Avoid N+1 I/O, unbounded materialisation, and repeated expensive work by construction. Measure before applying deeper performance tuning.

Format, lint, type-check, and test touched code with repository commands.

## Pre-commit

For projects using this Python baseline, `.pre-commit-config.yaml` must enable these hooks in this order:

1. `absolufy-imports` for absolute imports.
2. `black` with a line length of 79.
3. `pre-commit-hooks`: `trailing-whitespace`, `end-of-file-fixer`, and `debug-statements`.
4. `isort` with `--profile black` and line length 79.
5. `yesqa` to remove obsolete `# noqa` comments.
6. `flake8`, configured in `setup.cfg` with `max-line-length = 80`, `max-complexity = 20`, the shared ignore list, and `tests/*: E501`.

Set `fail_fast: true`, install the hooks with `pre-commit install --install-hooks`, and have CI rerun the same hooks. Do not merge with failing pre-commit checks.

## Style rules

- Avoid comments unless they state something code cannot express: an external constraint, a non-obvious business rule, or a link to an upstream bug or specification. Prefer clearer names, extraction, or simplification; never narrate what the code does.
- Use explicit, domain-specific names. Do not use `a`, `b`, `c`, `tmp`, `data`, `obj`, `res`, or single-letter names, except short comprehension indices. Name variables for their contents and functions for their action, beginning with a verb.
- Give every function one job at one abstraction level. Split functions whose names need “and” or whose steps would need section comments; extract named functions instead. This keeps one-unit-test-per-function practical.
- Type all public functions and prefer dataclasses to passing dictionaries for internal structured values.
- Never use `print()`; use a module logger, such as `logger = logging.getLogger(__name__)`.
- Write docstrings for rationale or behavioural contracts, never to restate the implementation.
