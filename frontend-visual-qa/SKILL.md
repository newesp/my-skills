---
name: frontend-visual-qa
description: Review or implement frontend UI/UX changes with visual QA. Use when Codex works on layout, responsive behavior, toolbars, buttons, modals, panels, forms, overflow/clipping, text fitting, overlap, drag/drop affordance, loading/download feedback, or any user-visible interface polish.
---

# Frontend Visual QA

Use this skill before changing or declaring complete any user-visible frontend UI. The goal is to fix the layout system, not just the visible symptom.

## Workflow

1. Identify the affected surface and parent container hierarchy.
   - Name the component/file and the UI region.
   - Include the immediate parent layout: flex, grid, absolute, modal, pane, or scroll container.

2. Diagnose the visual root cause before editing.
   - Check width constraints, `min-width`, `max-width`, `flex-shrink`, `flex-wrap`, grid tracks, `overflow`, positioning, z-index, line height, padding, and text length.
   - State one concrete hypothesis: for example, "buttons are clipped because a right-aligned flex row is wider than the pane and the parent hides overflow."

3. Choose a layout strategy that prevents repeat fixes.
   - Prefer container-level fixes over per-label tweaks.
   - Use grid tracks for a known fixed count of controls.
   - Use flex with explicit shrink/wrap rules for variable count controls.
   - Use menus or disclosure only when controls cannot reasonably fit.
   - Do not solve clipping by only shortening text unless text length is the actual root cause.

4. Preserve interaction semantics.
   - Keep existing click handlers, disabled states, titles/tooltips, keyboard behavior, and modal behavior unless the task asks to change them.
   - Buttons should keep icon plus text when requested by the product style.

5. Verify against an acceptance checklist before completion.

## UI Acceptance Checklist

For every changed UI surface, verify:

- No control is horizontally clipped.
- No text overlaps another element.
- Button text fits inside its button at the target pane width.
- Required controls remain visible without hidden horizontal overflow.
- Primary and destructive actions remain visually distinct.
- Tooltips or `title` attributes exist when labels are intentionally shortened.
- Empty, loading, disabled, and success/failure states still make sense.
- The fix addresses the parent layout constraint, not just one visible instance.

## When Browser Verification Is Needed

Use the Browser plugin or another available browser automation tool when:

- The task is a visual regression, screenshot bug, responsive layout issue, drag/drop affordance, canvas/3D rendering, or modal/popup positioning issue.
- The target URL is obvious or a local dev server is already running.
- The user explicitly asks to inspect or screenshot the UI.

When browser verification is not run, say why and provide the CSS/layout reasoning used instead.

## Reporting

In the final response, include:

- The visual root cause.
- The structural fix.
- The verification command or browser check performed.
- Any residual viewport or manual QA risk.
