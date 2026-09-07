---
name: python-quality
description: Write or review clear, typed, maintainable Python while respecting the project's existing formatter, linter, and type-checking setup. Use for Python implementation or review.
---

# Python quality

Use the project's configured formatter, linter, type checker, dependency manager, and supported Python version; do not introduce replacements merely for preference.

- Prioritise correctness and data safety, then clarity and simplicity; optimise only when a measured requirement justifies the added complexity.
- Use clear, domain-specific names and small functions with a single coherent responsibility. Prefer explicit data structures and typed interfaces over ambiguous dictionaries and hidden conventions.
- Type public APIs and important boundaries to the level supported by the project. Model expected failure paths explicitly rather than returning ambiguous sentinel values.
- Keep comments and docstrings for constraints, rationale, or behavioural contracts that code cannot express. Do not narrate obvious implementation.
- Use the project's logging convention rather than `print`; do not log secrets or sensitive data.
- Avoid N+1 I/O, unbounded materialisation, and repeated expensive work by construction. Measure before applying deeper performance tuning.

Format, lint, type-check, and test touched code with repository commands.
