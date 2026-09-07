---
name: api-contracts
description: Design or change stable HTTP or RPC interfaces with validated boundaries and compatible, observable contracts. Use for endpoints, request and response models, API errors, or versioning.
---

# API contracts

Follow the repository's framework and API conventions. Define the caller-facing contract before handler implementation.

- Specify request shape, required and optional fields, validation rules, authorization, success responses, expected errors, and pagination or idempotency semantics when applicable.
- Validate user and external input at the boundary. Convert it to explicit application types before business logic; never let raw payloads define policy.
- Keep error responses consistent and safe: expose actionable client errors, correlate unexpected failures, and do not disclose internals, secrets, or other users' data.
- Preserve backward compatibility unless a versioned breaking change is explicitly intended. Consider existing clients, defaults, nullability, ordering, rate limits, and retry behaviour.
- Document and test the observable contract. Include authorization, invalid input, conflict, and failure cases that matter to callers.

Keep HTTP/RPC concerns at the boundary; business logic should remain reusable by other transports and background jobs when practical.
