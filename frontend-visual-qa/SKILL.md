---
name: frontend-visual-qa
description: Design, implement, or review user-visible web or desktop UI. Use for editor and workflow consistency, fewer steps or windows, responsive layout, scrolling, interaction feedback, and visual regression work.
---

# Frontend Visual QA

Prioritize fluent tasks, intuitive operation, and consistent UI. Reduce unnecessary buttons, windows, and steps while keeping useful actions discoverable. Fewer visible controls are not an improvement if they add searching or clicks.

## Workflow

1. **Establish the baseline.** Identify the task, affected components, parent layout, and closest working reference surface. Inspect its interactions; name patterns to reuse and intentional differences. For defects, reproduce the problem and investigate the cause. Assessment-only requests produce recommendations without product changes.
2. **Define acceptance.** Use representative content and applicable empty, processing, edited, disabled, failed, partial, cancelled, and successful states. Include target viewport sizes and adverse transitions. Write short actions with observable outcomes, such as “Open history → Select a version → Verify the current input is unchanged.”
3. **Design the structure.** Apply the rules below. Inspect dimensions, shrink/wrap behavior, overflow, positioning, focus, and event routing. Fix parent layout constraints rather than special-casing one label. Shorten wording when it is redundant, not to conceal clipping.
4. **Verify the actual surface.** Use browser tools for web UI and native tools for desktop UI. Complete the task with representative data at normal and reduced sizes; exercise keyboard and other supported input methods. Inspect screenshots and interactions. Fix failures and repeat affected scenarios. Tests or a fitting screenshot alone do not establish usability.

When mockups are requested or project rules require approval, show important states and expanded tools before implementation. Reuse approval for the agreed scope. For design-only work, inspect the mockups and mark runtime acceptance as pending. If runtime access is unavailable, report the specific unverified paths.

## Task and Interface Consistency

- Arrange prerequisites and actions in task order, using the reading direction of the interface. Make the next action evident. Keep genuinely different tasks distinct; shared components need not force unrelated data into one editor.
- For consecutive operations on the same content, prefer one stable workspace. Import, edit, automated assistance, review, and save can update content and state without opening duplicate editors or confirmation pages. Preserve input, selection, scroll context, and user-adjusted pane visibility/size across transitions.
- Expose frequent actions directly. Put occasional tools behind a named entry point and expand them in the current workspace where practical. Specify how each tool opens, closes, and retains state. Choose side panels, inline bars, tabs, or other layouts for the task; no single arrangement fits every project.
- Allocate space to primary content. Use focused list/detail views and search/filtering when collection size warrants them. At reduced sizes, reflow, collapse secondary tools, or offer explicit pane navigation with retained context.
- Let background work batch internally without requiring users to repeat an action per batch. Offer whole-item operations and optional individual review when appropriate.

Example: import text → edit in the same workspace → run full-text assistance → review changes there → save for downstream use. An additional page that merely repeats the saved text adds no useful decision.

## Scrolling and Content Reachability

- Assign each overflow region a scroll owner and direction before implementation. In editor workspaces, keep navigation, toolbars, and primary actions stable; scroll long text, lists, tables, or previews within their content regions. Ordinary document pages and long forms may legitimately use page scrolling.
- Give scrollable content usable, bounded space. Avoid a same-axis outer workspace scroller competing with an inner editor by default. Where nesting is necessary, define behavior at scroll boundaries so users can reach both regions without accidental jumps or traps.
- Overflowing text must remain readable through its final line. Provide platform-appropriate scroll affordances; hiding a scrollbar or clipping overflow is not a layout fix. Prefer wrapping prose. Allow horizontal scrolling only for content that needs it, such as wide tables or code.
- Route wheel/touch input to the intended region and keyboard scrolling to the focused control. Global handlers must not steal scrolling from text areas. Fixed controls must not cover content or focused fields.
- Verify with content several times taller than the viewport: scroll to both ends using supported wheel/touch, scrollbar, and keyboard interactions; confirm only the intended region moves and primary actions remain reachable. Repeat after resizing and expanding tools. A visible scrollbar is not evidence that scrolling works.

## Necessary Feedback, Minimal Noise

Show information when it explains the current state, the result, a meaningful consequence, or an actionable next step.

- **Processing:** acknowledge the action promptly with a busy indicator or status. Prevent duplicate/conflicting actions. Show real progress when available and cancellation when supported.
- **Completion:** provide perceptible success, partial, cancelled, or failed feedback. A clearly updated result or inline status may suffice; routine success need not open a blocking alert. Make failures actionable and retain relevant input. Place field errors near the problem; use a navigable summary when errors span hidden regions.
- **Decisions:** explain unexpected consequences before committing them. Keep required consent and approval according to the project contract and explicit user instructions.
- **Noise:** omit repeated summaries, technical identifiers, obvious explanations, and options with no meaningful effect. Move details to an on-demand view. For example, an explicit AI proofreading action usually needs no adjacent “text will be sent to AI” reminder unless the actual scope or consent requirement warrants it.
- Show fields and actions applicable to the selected service, source type, and work state. Explain blocked actions where users can resolve the prerequisite.

## Project-Specific Data Contracts

Use the project's existing save, permissions, validation, and recovery rules. Do not introduce version history, approval workflows, autosave, or production snapshots merely to simplify UI.

- Label the scope and effect of save/apply/publish. Combine actions only when the intended workflow permits it, with consistent commit behavior. Failure cannot report success or advance to a ready state.
- Preserve input across navigation and failures. Before reload, replacement, or reauthentication would discard edits, use the project's retention mechanism or obtain an explicit discard decision. Keep destructive actions distinct from routine actions.
- Where versions or approvals exist, distinguish editing, preview, saved, and approved states; apply the project's invalidation rules after changes. Reject stale asynchronous results that would overwrite newer input.
- Where relationships exist, offer eligible choices with meaningful names/status; explain empty choices and consequences of clearing dependent selections. Returning from related-content editing preserves the original input.
- Where preview exists, preview current input without publishing it or bypassing permissions. Store sensitive drafts and credentials only through approved project mechanisms.

## Completion Evidence

For each affected rule, record observed evidence or the unverified condition:

- Complete the primary task and open/close affected tools; verify consistent placement, retained context, and clear action scope.
- Exercise long-content scrolling as specified above, plus reduced viewport and expanded-panel states. Confirm readable labels, no overlap/clipping, and reachable focus/actions.
- Exercise one meaningful adverse transition, such as a request failure or navigation with unsaved input; verify feedback and applicable data contracts.

Use isolated fixtures; change production content only when authorized. Retain regression tests for plausible state or data-loss bugs and actual-surface evidence for layout and interaction. Close only owned tabs and stop only owned test processes.

When spec/ticket maintenance is in scope, update the authoritative rules and identify superseded behavior. Consolidate recurring lessons into existing guidance rather than parallel copies.

Report the baseline or root cause, change or recommendation, exercised tasks/content sizes/viewports, and remaining verification gaps. Do not claim runtime completion from static mockups or tests alone.
