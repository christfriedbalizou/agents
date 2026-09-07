---
name: project-tooling
description: Maintain a project's repeatable build, dependency, container, CI, and release tooling without imposing a specific ecosystem. Use when changing developer commands, dependencies, CI, containers, or versioning.
---

# Project tooling

Existing repository tooling is the contract. Discover and use its documented entry points before adding a package manager, Makefile, task runner, container pattern, or release tool.

- Keep routine developer and CI actions reproducible through stable, documented commands. CI should run the same meaningful checks developers can run.
- Change dependencies deliberately: verify source, license, compatibility, maintenance, and security impact; update lockfiles or manifests according to the project's package-management rules.
- Make builds deterministic where practical. Keep container images minimal, cache-friendly, non-root where supported, and free of build-time secrets.
- Put configuration in explicit environment/config mechanisms rather than source edits. Document required variables without putting values or secrets in examples.
- Treat generated artefacts and version metadata according to repository policy. Do not commit generated output or revise versioning conventions without a deliberate project decision.

Validate the affected local workflow and relevant CI configuration after a tooling change.
