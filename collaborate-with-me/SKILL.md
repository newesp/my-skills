---
name: collaborate-with-me
description: Use when collaborating with this user on product planning, code changes, reviews, deployments, third-party API integrations, external side effects, or when requirements, mock-versus-real status, or verification need explicit handling.
---

# Collaborate With Me

Use this skill to match the user's preferred collaboration rhythm: clarify only what matters, distinguish planning from implementation, protect external side effects, and report status in a concise but explicit way.

## Collaboration Mode

- Treat explicit stage words as binding: "review", "評估", "建議", "更新 plan", "開始實作", "先問清楚再動", "commit and push".
- If the user asks for review or assessment, do not edit files unless they then ask to implement.
- If the user says to start implementation, proceed proactively after checking local context.
- If the user says uncertainty should be resolved first, pause before changing behavior that affects product flow, data flow, costs, credentials, publishing, or external services.

## Requirement Shape

When requirements involve a product flow, restate or internally structure them as:

- Step or screen
- User action
- Expected system behavior
- Behavior that must not happen
- External service or environment dependency

Prefer this structure for wizards, OAuth/login, publishing, imports/exports, API-key handling, cost-incurring API calls, preview/editing, and deployment tasks.

## Mock Versus Real

Actively distinguish:

- Real API/functionality already wired
- UI-only mock or placeholder
- Local-only behavior
- Behavior blocked by missing env vars or third-party setup

When asked "現在有哪些部分還是 mock?" or similar, inspect code and answer with concrete surfaces, files/routes when useful, and what is required to make each one real.

## External Side Effects

Do not trigger real external side effects without clear user intent at action time. This includes (examples below, not exhaustive):

- Publishing or broadcasting content externally — social platforms (e.g. LINE, Meta, Instagram), webhooks, feeds, or any outbound channel
- Making cost-incurring calls to paid third-party or LLM APIs
- Uploading personal/private files to third-party services
- Changing OAuth/app/channel/page/access settings for any external service
- Writing production secrets or deploying production (or production-adjacent) changes
- Writing to a shared or production database/datastore

It is acceptable to run local tests, builds, static checks, and non-mutating, non-billable API-shape checks. Before any live, paid, or production-adjacent action, confirm the target, intended change, and payload (if applicable) with the user first.

## Source Of Truth

Evidence priority (highest to lowest):
1. User-provided docs, cURL examples, Postman responses, screenshots, and observed app/runtime behavior
2. Official documentation or live local code/tool calls
3. Training-data memory (only when 1–2 are unavailable)

Rules:
- When user-provided real responses exist, match payloads/parsers to them exactly — do not "correct" them based on memory of the spec.
- For unstable details (third-party API/SDK behavior, runtime versions, config, MCP tool schemas): verify via official docs or a real tool call before relying on them. Do not answer from memory alone.
- Any claim not verified this way must be marked `[unverified]` inline, e.g.:
  `Assumes rate limit is 100 req/min [unverified — not found in provided docs]`
- Escalation rule:
  - If the unverified assumption affects correctness, security, or data integrity → stop and ask the user before proceeding.
  - Otherwise → proceed but document the assumption explicitly (don't silently bury it).

## Implementation Reporting

Final reports should be short and explicit:

- What changed
- What was verified
- What remains mock or requires environment/configuration
- Any command that failed or could not be run

For this user, do not bury important caveats. Call out required env vars and account setup by exact names when known.

## UI Feedback

When handling UI changes:

- Treat screenshots and annotated text as precise product direction.
- Preserve simple workflows and avoid redundant buttons.
- Prefer "what you see is what will be sent" for preview/edit experiences.
- If a label or badge has no user-facing function, remove it rather than explaining it in the UI.

## Relation To Superpowers And pr-readiness

This skill does not replace superpowers or pr-readiness. Use superpowers for generic engineering discipline such as brainstorming, TDD, visual QA, verification, and skill creation. Use `pr-readiness` for the engineering quality bar before requesting human review (docs, dead-code cleanup, build/lint/test verification, secrets scanning, self-review reporting). Use this skill for this user's communication preferences and project-specific collaboration safeguards.

When multiple apply, follow all of them:

- Superpowers governs the work method.
- pr-readiness governs the quality bar and checks before requesting review.
- Collaborate With Me governs how to communicate, when to pause, what to clarify, and how to report truthfully to this user — including how to summarize pr-readiness results for this user specifically.

## Useful Invocation Examples

- "Use $collaborate-with-me and review which parts are still mock."
- "Use $collaborate-with-me; first clarify the flow, then implement."
- "Use $collaborate-with-me for ai-social-publisher and do not trigger real publishing."
- "Use $collaborate-with-me to update plan.md, then wait before coding."
- "Use $collaborate-with-me for myBookLM — don't call any paid LLM API until I confirm which provider."
- "Use $collaborate-with-me for novel-generator and tell me what's still local-only mock vs actually wired up."
