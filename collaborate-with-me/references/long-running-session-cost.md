# Long-Running Session Cost Control

Use this policy for a written multi-task plan executed with subagents.

## Durable State and Handoff

- Keep a ledger containing completed commit ranges, the tested code commit,
  review verdicts, unresolved invariants, failed hypotheses, pending gates,
  and external blockers. Resume at the first incomplete gate.
- If a task or conversation is missing, recover from the ledger, Git history,
  and task metadata before rebuilding context or repeating work.
- Give agents task brief, report, and diff file paths. Use
  `fork_turns: "none"` instead of copying session history.
- Start a fresh session at a stable milestone when controller context becomes
  large.
- Before switching, write a handoff with: objective; repository, worktree,
  branch, and HEAD; plan and ledger paths; completed and pending gates; exact
  next action; latest verification evidence; model policy; allowed live
  actions; external blockers; dirty files; environment limitations; and a
  ready-to-paste startup prompt.

## One Review Loop per Task

1. Dispatch one implementer. It performs focused RED/GREEN tests and
   self-review; it does not dispatch nested reviewers.
2. Dispatch one controller-owned task reviewer. Require one bounded verdict
   containing all Critical, Important, and Minor findings.
3. Send all Critical and Important findings in one combined fix wave.
4. Run covering tests for the fix wave and perform one re-review.
5. If re-review still finds Critical or Important defects from the same
   invariant family, stop the loop. Checkpoint failed hypotheses, identify the
   shared architectural boundary, present bounded recovery options, and obtain
   user direction before another material change.
6. After the last material fix, run the final-head full suite, production
   build, diff check, and secret scan once before marking the task complete.

Documentation-only or test-only follow-ups do not trigger another broad review
unless risk changed.

## Explicit Model Policy

When available:

- Mechanical implementation: `gpt-5.6-terra`, medium reasoning.
- Multi-file integration, UI, concurrency, and task review:
  `gpt-5.6-terra`, high reasoning.
- Whole-branch architecture and security review:
  `gpt-5.6-sol`, ultra reasoning.

Every dispatch sets `model`, `reasoning_effort`, and
`fork_turns: "none"` explicitly. If a model is unavailable or quota-blocked,
report the fallback instead of silently inheriting the controller model.

The user does not need to switch the main-session model for each subagent.
Put this model policy in the startup prompt or handoff; the controller applies
it in each subagent dispatch.
