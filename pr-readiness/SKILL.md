---
name: pr-readiness
description: Use when a feature, module, or otherwise meaningful chunk of work is fully implemented and about to be handed off for human review — NOT after every small fix, one-line tweak, or intermediate push within an in-progress feature. Trigger on phrases like "這個功能做完了", "ready for review", "PR 準備好了", "before I open a PR", "before merging this feature", or when the agent itself is about to signal that a substantial piece of work is finished. Covers documentation updates, dead-code cleanup, build/lint/test verification, secrets/PII scanning, focused self-review, and an optional user-requested three-reviewer dispatch. Applies regardless of which user or project.
---

# PR Readiness

Use this skill to define the engineering quality bar a change must meet before requesting human review. This skill owns the completed-change handoff checklist; `collaborate-with-me` owns authorization, external effects, evidence, and reality-state tracking during the work.

## Scope Gate (check this first, every time)

This skill is for handing off a **completed feature or meaningful chunk of work** — not for every commit or push. Check scope before running anything below.

- **Run the full checklist when:** a requested feature/task is fully implemented and about to be handed to a human, a multi-file meaningful change is ready to be handed off for human review, or the user explicitly signals completion ("ready for review", "這個功能做完了").
- **Skip this skill entirely when:** the change is a typo fix, one-line tweak, or an intermediate commit/push that's part of a still-in-progress feature. Commit/push normally — running local build/lint is still fine practice, but do not dispatch parallel reviewers (step 7) for this.
- **If unsure which bucket a change falls into:** ask once rather than guessing. Skipping the checklist when it is needed costs correctness. Do not infer a request for parallel reviewers from scope or risk; dispatch them only when the user explicitly requests a three-reviewer review.

## Documentation Standard

**Docs (README, design notes, API references):**
Update when a change affects how someone would use, operate, or reason about the system — new/changed public interface, changed operational flow, or a non-obvious decision that future readers would otherwise have to reverse-engineer. Skip for internal refactors with no external-facing effect.

**Code comments (docstrings, JSDoc, etc.):**
Add when purpose, parameters, return value, side effects, or failure modes are NOT obvious from the code/types/names alone. Skip on self-explanatory code (e.g. trivial getters/setters) — comments there add noise, not clarity.

- Default to concise (1–2 lines) for straightforward cases.
- Expand to explain *intent and tradeoffs* only when the "why" isn't derivable from the "what" (e.g. non-obvious algorithm choice, workaround for an external bug, deliberate deviation from the obvious approach).

**Bad example:** `// see JIRA-1234` alone, with no explanation of what changed or why.
**Good example:** `// Retry 3x with backoff — upstream API rate-limits bursts (JIRA-1234 has full incident log)`

## Before Requesting Human Review

**Verify**
1. Run the project's real build, lint, and test commands. Include actual output, or the relevant passing summary / failing lines when output is long.
2. Smoke-test the real runtime path against test/staging endpoints. Never use production side-effecting endpoints without explicit approval.

**Clean up (scoped to this diff)**
3. Remove dead code introduced or touched by this change: unused files, exports, enum members, fields written but never read, stale comments. Do not do drive-by cleanup outside this PR's concern — file it separately instead.
4. Update docs per the Documentation Standard above.

**Scope & safety**
5. Confirm this PR addresses one concern only; split it if it doesn't.
6. Scan the diff for secrets, PII, and customer-identifiable data. If found, stop and remove/rotate before proceeding — do not merely note it.

**Review**
7. Run a focused self-review of the completed diff. Only when the user explicitly requests a three-reviewer review, dispatch three parallel reviewers for (1) correctness and logic, (2) security and performance, and (3) maintainability, naming, and file locations. Fix all Critical and Important findings from the reviews performed, then run one focused confirmation pass. Do not dispatch or re-dispatch subagents unless the user explicitly requested the three-reviewer review for this handoff.

**Finalize**
8. If this is your own PR branch, squash noisy iteration commits into meaningful commits before handoff. Force-push only to your fork/branch, never upstream/shared branches.

**Report**
9. Post a self-review report covering: commands run and their outputs/summaries, dead code removed, docs updated, scope/security check results, reviewer findings, fixes made, and deferred items with reasons.

## Exceptions (skip only when explicitly justified)

Some steps may be skipped for low-risk changes, but the skip itself must be stated in the report — never silently omitted.

- **Docs-only / comment-only PR** (no code logic changed): skip build/lint/test *execution* only if CI would trivially pass; still run lint on the touched files.
- **Hotfix under explicit time pressure** (approved by a human): use a single fast correctness and security self-review. If the user requested a three-reviewer review, obtain approval before reducing or deferring it and record the decision in the report.
- **Config/dependency-version bump with no behavior change**: smoke-test step may be skipped if CI already covers the affected path; state which CI job substitutes for it.

When this skill runs, never skip: secrets/PII scan, and "one PR one concern" scope check — these apply regardless of change size or urgency.
