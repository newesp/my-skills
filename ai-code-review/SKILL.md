---
name: ai-code-review
description: Review a completed feature, ticket, branch, or AI-generated diff against three independent axes: Spec Compliance, Engineering Standards, and Defensive Security. Use after a meaningful unit of work, before merge/PR/release, or after a large AI-generated change. Review only the diff since a fixed point, then aggregate findings without collapsing the three axes into a single score.
---

# AI Code Review Skill

## Purpose

Review the changes between a fixed point and `HEAD` across three independent axes:

1. **Spec Compliance**
   - Does the implementation match the originating requirement, issue, task, or spec?
   - Are any requested behaviors missing, partial, or incorrect?
   - Did the implementation introduce scope creep?

2. **Engineering Standards**
   - Does the change follow the repository's documented conventions and architecture?
   - Does it introduce maintainability issues or code smells?
   - Does the implementation fit expected file locations, naming, responsibilities, and abstractions?

3. **Defensive Security**
   - Does the implementation follow secure coding practices?
   - Are authentication, authorization, validation, SQL safety, XSS prevention, CSRF, secrets handling, session behavior, and business logic protections implemented correctly?
   - This axis is defensive review only. Do not perform penetration testing, vulnerability scanning, exploit generation, destructive testing, or offensive payload execution.

Keep the three axes separate so one dimension cannot hide failure in another.

---

# When to Use

Use this skill after a meaningful unit of work, including:

- a feature is completed;
- a ticket is completed;
- an AI coding agent changed many files;
- a branch is ready for merge;
- a PR is ready for review;
- a release candidate is ready;
- the user asks to review changes since a commit, branch, tag, or merge-base.

Do not require running this skill before every small commit.

---

# Process

## 1. Pin the Fixed Point

The user should provide a fixed point such as:

```text
main
HEAD~3
abc123
release/v1.2
```

Resolve it:

```bash
git rev-parse <fixed-point>
```

Capture:

```bash
git diff <fixed-point>...HEAD
git log <fixed-point>..HEAD --oneline
```

Use three-dot diff so the comparison is made against the merge-base.

If the fixed point is invalid, stop and report it.

If the diff is empty, stop and report that there are no changes to review.

Do not silently review the entire repository when the requested scope is a diff.

---

## 2. Identify the Spec Source

Find the originating requirement in this order:

1. issue/ticket references in commit messages;
2. a spec path supplied by the user;
3. relevant files under:
   - `docs/`
   - `specs/`
   - `.scratch/`
   - `temp/`
4. branch-name-matching requirement documents;
5. project context files such as `CONTEXT.md` when they define required behavior.

If no spec exists, explicitly mark the Spec axis as:

```text
No explicit spec available
```

Do not invent requirements.

---

## 3. Identify Engineering Standards Sources

Look for repository rules such as:

```text
CONTRIBUTING.md
CODING_STANDARDS.md
STYLEGUIDE.md
ARCHITECTURE.md
CONTEXT.md
README.md
AGENTS.md
docs/
```

Repository-documented standards override generic review heuristics.

Skip issues that automated tooling already deterministically enforces unless the diff reveals a design-level problem beyond the tool's scope.

---

# Engineering Smell Baseline

Use these only as heuristics, never as automatic violations.

## Mysterious Name
A name does not communicate intent.

Recommended action:
- rename to expose the real responsibility.

## Duplicated Code
The same logic shape appears multiple times.

Recommended action:
- extract only when the duplication represents the same responsibility.

## Feature Envy
A method reaches deeply into another object's data.

Recommended action:
- move behavior closer to the data it primarily operates on.

## Data Clumps
The same group of fields repeatedly travels together.

Recommended action:
- consider a dedicated value object or request type.

## Primitive Obsession
Strings, integers, or booleans are used where a domain concept would be clearer.

Recommended action:
- introduce a small type only when it reduces ambiguity.

## Repeated Switches
The same status/type branching appears repeatedly.

Recommended action:
- centralize the rule or introduce a domain abstraction.

## Shotgun Surgery
One logical change requires edits scattered across unrelated places.

Recommended action:
- consolidate responsibility.

## Divergent Change
A file changes for several unrelated reasons.

Recommended action:
- split responsibilities.

## Speculative Generality
Abstraction exists for requirements that do not exist.

Recommended action:
- simplify until a real need appears.

## Message Chains
Callers navigate long object chains.

Recommended action:
- hide traversal behind an appropriate domain method.

## Middle Man
A class/function mostly delegates with no meaningful responsibility.

Recommended action:
- remove unnecessary indirection.

## Refused Bequest
An implementation inherits behavior it does not actually need.

Recommended action:
- replace inheritance with composition or a narrower interface.

The repository's documented architecture always wins over this baseline.

---

# 4. Run Three Independent Review Axes

Run the three axes independently.

If sub-agents are available and the user explicitly requested multi-reviewer execution, run them in parallel.

Otherwise perform the three passes sequentially while preserving separate findings.

Do not merge findings while reviewing.

---

# Axis A — Spec Compliance Review

Review only the changed behavior against the originating requirement.

Check:

- requested behavior that is missing;
- partially implemented requirements;
- behavior implemented incorrectly;
- requirements that appear satisfied but violate the intended semantics;
- scope creep;
- unnecessary features;
- changed existing behavior not requested by the spec;
- edge cases explicitly required but not covered;
- conflict with existing production behavior;
- migration or compatibility assumptions that contradict the spec.

For each finding include:

```text
Severity:
File / hunk:
Spec requirement:
Observed implementation:
Why it differs:
Recommended correction:
```

Quote or reference the relevant spec section when available.

Do not review style or security here unless it directly causes a spec violation.

---

# Axis B — Engineering Standards Review

Review the diff against repository standards and the smell baseline.

Check:

- naming;
- file placement;
- architecture boundaries;
- responsibility separation;
- duplicated logic;
- unnecessary abstractions;
- coupling;
- error handling;
- logging;
- test organization;
- maintainability;
- consistency with existing patterns;
- migration ownership;
- domain invariants;
- configuration placement;
- dead code;
- stale docs;
- stale scaffolding.

For each finding distinguish:

```text
Hard violation
```

from:

```text
Judgement call
```

A hard violation must cite a documented repository rule.

A smell is always a judgement call unless the repository explicitly forbids it.

---

# Axis C — Defensive Security Review

This is a defensive secure-code review.

Do not:

- perform active vulnerability scanning;
- generate exploit payloads;
- attempt authentication bypass;
- perform destructive testing;
- attack production services;
- probe unrelated third-party systems;
- exfiltrate secrets;
- perform penetration testing.

Review the implementation for secure patterns.

## SQL Safety

Verify:

- ORM/query builder parameter binding is used;
- raw SQL does not concatenate untrusted input;
- dynamic columns/order/filter expressions are allowlisted;
- database identifiers are not built from arbitrary request input.

## XSS Prevention

Verify:

- templates escape user-controlled output;
- raw HTML rendering is justified and sanitized;
- DOM insertion does not use unsafe HTML APIs with untrusted content.

## Authentication

Verify:

- protected routes require authentication;
- invalid/expired tokens are rejected;
- login regenerates session identifiers where applicable;
- logout invalidates session state;
- OAuth/JWT validation checks signature and required claims.

## Authorization / IDOR

Verify:

- admin-only operations are enforced server-side;
- ownership checks exist;
- resource IDs cannot be used to access unrelated users' data;
- restricted/demo identities cannot invoke prohibited actions.

## CSRF

Verify session-based state-changing browser requests are protected.

## Mass Assignment

Verify privileged fields cannot be modified through uncontrolled request input.

Examples:

```text
role
status
owner_id
is_admin
security settings
audit fields
```

## Input Validation

Verify validation exists for:

```text
UUIDs
IDs
enums
numeric values
length limits
required fields
JSON shape
arrays/objects
dates
URLs
```

## SSRF Risk

If outbound URLs are accepted:

- require validation;
- restrict destinations as needed;
- prevent access to localhost/private/link-local/metadata targets when relevant.

Do not actively probe networks.

## Open Redirect

Verify redirect targets are constrained to trusted destinations.

## File / Path Safety

Verify:

- file paths remain inside intended roots;
- downloads require authorization;
- uploads cannot create executable or unsafe paths;
- traversal is prevented.

## Sensitive Information Exposure

Review:

```text
.env.example
source code
logs
exceptions
debug output
CI logs
README
client bundles
configuration
```

for accidental secrets.

Do not print real secrets into findings.

## Session / Cookie Configuration

Verify production settings for:

```text
Secure
HttpOnly
SameSite
session invalidation
debug disabled
trusted proxies
rate limiting
```

where applicable.

## Business Logic Security

Review domain invariants such as:

- legal state transitions;
- privilege boundaries;
- destructive actions;
- totals/pricing;
- ownership;
- duplicate submissions;
- replay-sensitive operations.

For each security finding include:

```text
Severity:
File / hunk:
Security concern:
Why it matters:
Defensive correction:
Regression test:
```

---

# 5. Severity

Use:

## Critical
Immediate risk of major compromise, privilege escalation, sensitive-data exposure, remote execution, or destructive behavior.

## Important
Material correctness, security, architecture, or spec issue that should be fixed before merge/release.

## Moderate
Real issue with limited impact or meaningful preconditions.

## Low
Minor maintainability/hardening issue.

Do not inflate severity.

---

# 6. Fix Policy

After review:

- Fix all confirmed **Critical** findings.
- Fix all confirmed **Important** findings.
- Do not automatically fix Moderate/Low findings unless:
  - the user asked;
  - the fix is trivial and clearly safe;
  - leaving it would create inconsistency.

For every fix:

1. preserve intended behavior;
2. make the smallest correct change;
3. add/update focused regression tests where appropriate;
4. run related tests;
5. run the full regression suite.

Do not broaden scope unnecessarily.

---

# 7. Confirmation Pass

After Critical and Important fixes:

Run one focused confirmation pass against the changed diff.

Verify:

```text
Spec finding fixed
Standards finding fixed
Security finding fixed
Related tests pass
Full regression suite passes
```

Do not recursively spawn new reviewers unless the user explicitly asked for repeated multi-reviewer review.

---

# 8. Final Report

Keep the three axes separate.

Use:

```text
# AI Code Review

Fixed Point
- <fixed-point>

Commits Reviewed
- ...

## Spec Compliance
- findings...

## Engineering Standards
- findings...

## Defensive Security
- findings...

## Fixes Applied
- ...

## Tests
- focused:
- regression:
- CI:

## Remaining Risks
- ...

Summary
- Spec: X findings, worst = ...
- Standards: X findings, worst = ...
- Security: X findings, worst = ...
```

Do not collapse everything into a single numeric score.

A change can legitimately be:

```text
Spec pass / Standards fail / Security pass
```

or:

```text
Spec fail / Standards pass / Security pass
```

The separation is intentional.

---

# Completion Criteria

The review is complete when:

- the fixed point is valid;
- the diff scope is explicit;
- the originating spec was identified or explicitly marked unavailable;
- repository standards were identified;
- all three review axes were completed independently;
- Critical and Important findings were fixed;
- focused tests pass;
- the full regression suite passes;
- a confirmation pass was completed;
- remaining risks are documented.

Do not claim the code is globally "secure", "bug-free", or "fully correct".

Report what was reviewed, what was fixed, and what remains outside scope.
