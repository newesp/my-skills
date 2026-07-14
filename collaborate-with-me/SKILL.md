---
name: collaborate-with-me
description: Use when collaborating on planning, implementation, review, debugging, deployment, migrations, third-party integrations, credentials, production incidents, or work whose prerequisites, external side effects, real-versus-mock state, or verification status must be explicit.
---

# Collaborate With Me

Verify reality before changing implementation. Never equate code completion with a verified user outcome.

## Respect the Requested Stage

- For review, explanation, diagnosis, or status: inspect and report; do not edit or trigger external changes.
- For planning: resolve material uncertainty and get approval before implementation.
- For implementation: stay within approved scope and preserve unrelated work.
- Treat commit, push, deploy, migrate, publish, send, connect, revoke, secret changes, and production writes as separate actions unless clearly requested.
- Follow the newest instruction when it replaces earlier scope; combine it when it adds scope.

## Reality Preflight

Before work involving runtime behavior, data, deployment, or third parties, establish:

```text
Goal:
Verified facts:
Unknown assumptions:
External prerequisites:
Allowed live actions:
Definition of done:
```

Inspect user evidence and the actual system. Verify relevant prerequisites independently: environment, schema, provider app, callback, permission, account, billing, target, and runtime. Existing credentials or working behavior never prove that a different credential, integration, environment, or deployment prerequisite exists.

Mark material facts `[verified]`, `[missing]`, `[unverified]`, or `[blocked]`. Resolve uncertainty affecting correctness, security, data, cost, or external state before implementation.

## Keep Reality States Separate

Distinguish production behavior from tests, mocks, local behavior, missing configuration, deployed-but-unsmoked behavior, and untested external integrations. Prefer evidence in this order:

1. User observations, screenshots, logs, responses, and documents.
2. Current code, read-only checks, and official documentation.
3. Memory, labeled `[unverified]` when material.

For changing third-party behavior, check official sources or a safe live shape. Inspect secret presence, names, targets, or schemas without exposing values.

## Guard External Effects

Do not trigger publishing, messages, paid calls, private uploads, account changes, production writes, migrations, deployments, or secret changes without clear authorization for that action. Confirm the target, environment, change, and payload when relevant. A request to finish does not broaden authority.

For configuration sync, preserve the canonical file, write to a non-loaded staging file, compare names and structure without revealing values, then apply only after approval.

## Define Product and Failure Contracts

Capture the user action, expected result, visible failure, forbidden behavior, compatibility needs, and external dependencies.

- Test the production boundary, not only injected helpers. Prefer behavior or rendered-UI tests over source-string assertions.
- Expose safe capability readiness for optional features instead of offering an action that can only fail generically.
- Keep user errors actionable. Log non-sensitive categories and request IDs, never secrets, private provider bodies, or customer data.

## Verify the Final State

- Scale review to risk: focused self-review for small changes; independent review for meaningful changes; subsystem and final review for credentials, migrations, concurrency, publishing, or production data.
- After production changes, run a final-head gate: relevant and full tests, lint or type checks, production build, diff and secret scan, and prerequisite checks.
- A later production or configuration change invalidates earlier build, deployment, and smoke evidence. Re-run affected checks.
- When authorized, verify the deployed revision and safe runtime path. Record limitations instead of substituting tests for a blocked live check.
- Do not repeat broad review after documentation-only or test-only follow-ups unless risk changed.

## Report Precisely

```text
Code:
Tests/build:
Deployment:
Production smoke:
External integration:
Remaining prerequisites or blockers:
```

Use `complete` only for the highest verified level. Keep updates to current gate, completed evidence, blocker, and next gate. Ask only when the answer changes scope, safety, cost, data, or external state. Lead the final report with the outcome, then what changed, verification, unverified or mock parts, failed commands, and next action.

Return to Reality Preflight when reasoning becomes: "it worked before, so configuration exists"; "tests passed, so production works"; "deployed means the user flow works"; "the generic error probably comes from this layer"; or "review authorizes a fix or deployment."

Use specialized engineering skills for implementation methods; this skill supplies collaboration, evidence, readiness, and external-side-effect gates.
