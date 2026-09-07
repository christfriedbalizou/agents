---
name: debugging
description: Diagnose incidents and unexpected behaviour with an evidence-first, minimally invasive investigation. Use before proposing a cause or fix for a bug, error, or wrong result.
---

# Evidence-first debugging

Separate facts from hypotheses. Every finding should point to evidence: a reproduction, log or trace, metric, read-only data query, source location, or deployment record. Do not infer identifiers, timestamps, configuration, or causes.

1. State the symptom precisely: expected and actual behaviour, affected entity or request, environment, and time window.
2. Build a timeline: first known-bad, last known-good, and deployments, configuration, data, or upstream changes between them. Normalise timezones.
3. Gather the smallest relevant evidence from application telemetry, logs, traces, metrics, read-only data, and code. Use bounded queries and preserve the query or command used.
4. Form hypotheses only after evidence exists. Test each against the timeline and observed data; discard those that conflict with facts.
5. Report the failure scenario, impact, root cause or remaining uncertainty, evidence, and proposed fix separately.

Production investigation is read-only unless the user explicitly authorizes a scoped mutation. Never place credentials or sensitive production data in source control, chat, fixtures, or investigation notes. Keep incidental findings out of the main diagnosis; record and triage them separately.
