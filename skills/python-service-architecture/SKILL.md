---
name: python-service-architecture
description: Structure Python service business logic, persistence, and integrations behind explicit boundaries. Use when designing or reviewing service-layer code, data access, or external clients.
---

# Python service architecture

Use the project's established architecture when one exists. Otherwise keep business policy separate from HTTP, task-runner, ORM, and SDK concerns so it is testable and portable across frameworks.

- Keep transport handlers thin: validate and translate the request, call the application/service layer, and map expected errors to the transport contract.
- Put business rules in services or use cases with explicit inputs and outputs. They should not depend directly on request globals, framework objects, or secret/config lookup.
- Isolate database, cache, filesystem, and network I/O behind repositories, gateways, or adapters with clear contracts. Set explicit timeouts for outbound calls and surface actionable typed failures.
- Return domain objects or well-defined result types from lower layers, not framework response objects, raw cursors, or unvalidated payloads.
- Inject dependencies at composition boundaries so unit tests can use fakes. Keep transactions and consistency boundaries explicit when multiple writes form one unit of work.

Do not create layers mechanically. Add a boundary where it clarifies ownership, contains I/O, or makes important behaviour independently testable.
