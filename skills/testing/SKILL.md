---
name: testing
description: Design and maintain reliable automated tests for changed behaviour and important failure paths. Use when adding, changing, or reviewing tests, fixtures, or test infrastructure.
---

# Automated testing

Follow the repository's test layout, commands, and test pyramid. Test behaviour and contract, not implementation trivia.

- Add focused coverage for new or changed behaviour, including relevant validation, authorization, error, retry, idempotency, concurrency, and recovery paths.
- Keep unit tests fast and deterministic by injecting or faking I/O boundaries. Use integration or end-to-end tests where the contract depends on real components, serialization, persistence, or UI behaviour.
- Use synthetic, minimised fixtures. Never use production credentials, customer data, or live production services in automated tests.
- Assert outcomes that users or callers can observe. Avoid brittle assertions on incidental ordering, internal calls, timestamps, or generated text unless those are contractual.
- Run the narrowest relevant tests while developing and the repository's required broader checks before completion. Diagnose flaky tests rather than hiding, skipping, or weakening them.

When a defect is fixed, add a regression test when it is practical and would have caught the reported behaviour.
