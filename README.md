# Agents

A stack-neutral, opinionated foundation for repositories that use Codex-style agent instructions and reusable skills. Its `AGENTS.md` and skills deliberately express strong engineering defaults; adapt them to each project's facts and constraints. They intentionally do not prescribe an organisation's libraries, cloud provider, CI product, or deployment topology.

## Contents

- `AGENTS.md` is an opinionated repository-level operating posture. Start a project by copying it to that project's root and adding the project's own facts.
- `skills/` contains focused, opinionated, independently selectable skills. Every skill is a standard skill directory with a `SKILL.md` entry point.

| Skill | Use it for |
| --- | --- |
| `api-contracts` | Designing or changing HTTP/RPC interfaces and their contracts |
| `debugging` | Evidence-first diagnosis of an incident or unexpected behaviour |
| `delivery-status` | Evidence-based delivery and release-status reporting |
| `git-workflow` | Commits, branches, pull requests, and review readiness |
| `github-workflow-creator` | Secure, quality-gated GitHub Actions and trusted bot automation |
| `project-tooling` | Build/test commands, dependencies, CI, containers, or release tooling |
| `python-quality` | Writing or reviewing Python code |
| `python-service-architecture` | Python business logic, persistence, or integrations |
| `testing` | Adding, changing, or reviewing automated tests |

## Use in a project

1. Copy `AGENTS.md` into the project root.
2. Add a small project-specific section: mission, source-of-truth documents, supported commands, architecture constraints, data/security boundaries, and deployment authority. Keep this information factual and current.
3. Install or copy only the skills that suit the project into the agent's skill directory. Skills are intentionally additive; a Flask, Django, FastAPI, or non-Python repository should select its own framework-specific guidance.
4. Put narrower instructions in a nested `AGENTS.md` next to specialised code or infrastructure. Do not duplicate broad policy in every directory.

## Design rules

The baseline does not prescribe a web framework, ORM, database, package manager, formatter, test runner, cloud, Kubernetes distribution, migration tool, or CI system. Existing project conventions win. Add a new skill only for guidance that is reusable, specific enough to change an agent's decisions, and not better expressed as local project documentation.

Before adopting changes, read the whole affected skill and validate it against real work. Keep secrets, customer data, internal endpoints, production identifiers, and organisation-only tools out of this repository.

## Maintenance

Treat this repository as a conservative baseline. Improve a skill after a repeatable lesson, not after a one-off preference. Keep each skill narrowly scoped, describe when it applies, and avoid turning local conventions into universal rules.

## License

See [LICENSE](LICENSE).
