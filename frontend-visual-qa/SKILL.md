---
name: frontend-visual-qa
description: Design, implement, or review user-visible frontend changes. Use before building admin editors or multi-step forms, when matching an existing UI pattern, and for responsive layout, overflow, interaction feedback, or visual regression work.
---

# Frontend Visual QA

Use this skill before implementation and again before declaring a frontend change complete. Passing tests and fitting the viewport prove neither task usability nor consistency. Carry established UX decisions into the first delivery; new requirements or observed usage can still justify later changes.

## Workflow

### 1. Establish the interaction baseline

- Identify the affected component/file, user task, and parent layout: flex, grid, modal, pane, or scroll container.
- Inspect the closest existing working surface in the project, including its actual interactions. Record the patterns to reuse and explain material deviations. If none exists, choose a conservative baseline and state that limitation.
- Reuse established controls and behavior where contracts match. Keep domain-specific editors distinct; reuse is not a reason to impose another feature's fixed fields or build a generic editor framework.
- For an assessment-only request, inspect and recommend without changing product code or live data.

**Gate:** Name the reference surface, reusable patterns, task sequence, and intentional differences before editing. For a defect, reproduce it and state the cause before choosing the fix.

### 2. Define acceptance around tasks and content scale

- Cover initial/empty, loading, populated, edited/unsaved, disabled, validation failure, request failure, and success states that apply.
- Use representative content volume, long titles/body text, relationships, and version history—not only one short item. State the chosen volume and why it exercises the likely pressure points.
- For collection editors, use a list plus a focused editor or an established equivalent. Separate different content types into workspaces when their tasks differ. Provide search/filtering when the expected volume makes scanning impractical.
- On narrow screens, provide an explicit list/detail path, return navigation, and retained selection/input/scroll context. A page without horizontal overflow can still be unusably long.
- Follow the task order in the form: primary content first, relationships next, management metadata later. Explain missing prerequisites where the user encounters them.

**Gate:** Define observable desktop and mobile task outcomes before implementation, including how the user finds, edits, saves, and recovers from errors.

### 3. Fix the structure while preserving contracts

- For visual defects, inspect width constraints, `min-width`, `max-width`, shrink/wrap rules, grid tracks, overflow, positioning, z-index, line height, padding, and content length. State a concrete hypothesis.
- Prefer container-level fixes: grid tracks for a known fixed control count; explicit flex shrink/wrap for variable controls; menus or disclosure when needed. Shorten labels only when wording is the actual cause, not to hide a layout defect.
- Preserve permissions, data ownership, validation, versioning, approval invalidation, safety masking, and explicit save/publish semantics. UI simplification must not silently introduce autosave, automatic publication, or a new data model.
- Preserve keyboard, focus, modal, disabled, and tooltip behavior; retain icon plus text when required by the product style.
- For editorial, relationship, or versioned forms, apply the [editor safeguards](#editor-safeguards).

**Gate:** The chosen structure handles the representative content and known states without special-casing one label or dropping a product rule.

### 4. Exercise the actual surface

Use an available browser tool for non-trivial frontend implementation or UX assessment. Run the relevant application and inspect screenshots as well as interactions; tests, source inspection, and screenshots alone are insufficient.

- Use an isolated environment and representative fixtures; keep production content and publication untouched unless explicitly authorized.
- Complete the primary task at desktop and narrow/mobile widths. Exercise applicable search, selection, editing, relationships, preview, save, publication, error correction, and return navigation.
- Check a meaningful adverse transition: for example, change versions with unsaved input, switch a relationship to one with no eligible content, or correct a validation error hidden in another editor.
- Verify keyboard focus and reachable controls, not merely DOM presence. Capture the relevant content, not only the page heading.
- Fix failures and repeat the affected scenario. Keep regression tests for plausible data-loss or state-transition bugs; use browser evidence for visual/task usability.

**Gate:** Each affected acceptance item below has observed evidence. If runtime access is unavailable, finish static assessment, state exactly what could not be exercised, and label runtime acceptance as unverified rather than complete. Close only owned tabs and stop only owned fixtures.

## Editor Safeguards

Apply these when the surface edits collections, relationships, drafts, or published content:

- **State and scope:** Distinguish the loaded/editing version, current published version, unsaved changes, and pending approval. Label whether save/publish applies to one item or the whole edition; editing one visible item must not imply single-item persistence.
- **Action reachability:** Keep the primary save action easy to find without traversing unrelated records. Sticky controls are optional; if used, ensure they do not cover focused fields or content on small screens.
- **Input protection:** Switching items or workspaces preserves unsaved input. Switching/reloading versions or leaving the editor requires explicit discard confirmation when input would be lost. Remove destructive actions from the primary action hierarchy; show affected references before removal or disabling.
- **Relationships:** Show eligible choices with meaningful names and status. Explain an empty choice list and offer the next step. Warn before a relationship change clears existing selections; provide a return path from related-content editing that retains the original input.
- **Error recovery:** Put useful errors beside fields and provide a summary that opens the relevant item and focuses the problem. Preserve input after failures; a disabled publish button needs an actionable explanation.
- **Preview and approval:** For reader-facing content, preview current unsaved input without publishing or bypassing permissions/safety. Clearly label draft preview. Keep professional/editorial confirmation separate from mere format validity.
- **Reauthentication:** When fresh login is required, explain how pending edits will be retained or require an explicit choice before a navigation that discards them. Do not persist sensitive drafts in browser storage without an approved policy.

## UI Acceptance Checklist

For every changed surface, mark each applicable item with observed evidence; explain any non-applicable item:

- No horizontal clipping, overlapping text, or controls hidden by overflow; labels fit the target container. Intentionally shortened labels remain understandable and have an accessible full name or explanation.
- The established interaction baseline is retained, or deviations have a task-based justification.
- Representative content can be found and edited without scrolling through unrelated full forms.
- Empty, loading, disabled, failure, success, and unsaved states explain what happened and what to do next.
- Save/publish scope and current version/state are clear; primary and destructive actions are distinct.
- Applicable editor safeguards survive actual state transitions, including recovery and unsaved-input protection.
- Keyboard and mobile users can complete the same task, with predictable focus and return navigation.
- The fix addresses the layout or interaction structure, not just one screenshot.

## Preventing Repeated UX Rework

Treat previously accepted interaction patterns as a baseline, not optional polish. When a recurring defect exposes a missing rule, update the existing authoritative design guidance/checklist and the reusable behavior where appropriate; avoid parallel copies of the same rule. A behavior fix earns a regression check, while visual consistency earns an actual-surface comparison. Promise adherence to this process and its evidence, not that future product changes will never require redesign.

## Reporting

In the final response, include:

- The reference pattern and observed usability issue or visual root cause.
- The structural change made, or the prioritized recommendation for assessment-only work.
- The exercised tasks, content volume, viewports, and relevant verification command/browser evidence.
- Any unverified state, intentional deviation, or residual manual QA risk. Do not claim general UX completion from tests or a no-overflow check alone.
