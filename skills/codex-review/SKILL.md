---
name: codex-review
description: Get an outside second opinion from Codex CLI on code or on a plan/design. Use when the user names Codex or asks for a second opinion ("codex 리뷰", "세컨드 오피니언"), or wants a devil's advocate attack on a plan ("악마의 대변자", "devil's advocate").
---

# codex-review

Codex is a **second opinion**, not an authority. You run it read-only, then judge every point it makes yourself.

## 1. Pick the mode

- **review**: target is code (uncommitted diff, a branch, a commit, or named files).
- **design**: target is a plan, design, architecture, or decision. Codex reviews it as a senior engineer: what holds, what breaks, what is missing.

## 2. Write the brief

Write the prompt to `<scratchpad>/codex-brief.md`. Codex starts cold: it sees the files in its working dir, nothing from this conversation. The brief carries the goal, constraints already decided, and what is in or out of scope.

**review brief** ends with:

> Review for correctness bugs, security issues, and broken edge cases. For each finding: file:line, the concrete failing input or state, severity (high/med/low). Skip style nits. If nothing is wrong, say so.

**design brief** pastes the plan verbatim and ends with:

> You are a senior engineer reviewing this plan before it is built. Judge it on its merits: agree where it is right, push back where it is wrong. Give, in order:
> 1. Verdict: proceed / proceed with changes / rethink, with a one-line reason. A "proceed" still names the single biggest risk.
> 2. Risks: what fails, under what concrete scenario, severity (high/med/low). Ranked.
> 3. Gaps: requirements, cases, or operational concerns (migration, rollback, monitoring) the plan does not address.
> 4. Assumptions the plan depends on. Mark which you checked against the code and which you could not.
> 5. Alternatives: only if materially simpler or safer. State the tradeoff.
> 6. Keep: decisions that are sound, and why, so they are not churned.
> No summary of the plan.

When the user asks for devil's advocate, append to the design brief:

> Then argue the strongest case against the whole plan: the scenario where it is the wrong call entirely.

## 3. Run

Read-only sandbox, ephemeral session, final message only into a file. Full log goes to a file, never into context.

Model is pinned so a change to `~/.codex/config.toml` does not silently change review quality. Effort: `medium` by default; `high` for design mode, cross-file or concurrency-heavy diffs, or when the user asks.

```bash
OUT=<scratchpad>/codex-out.md
M=(-m gpt-6.1-sol -c model_reasoning_effort=medium)   # or =high, see above
# Files / plan / non-git dir:
codex exec "${M[@]}" -s read-only --ephemeral --skip-git-repo-check -C <project_dir> \
  -o "$OUT" - < <scratchpad>/codex-brief.md > <scratchpad>/codex-log.txt 2>&1
# Git diff review (built-in reviewer; brief becomes extra instructions):
codex exec review "${M[@]}" --uncommitted --ephemeral -o "$OUT" "$(cat <scratchpad>/codex-brief.md)" \
  > <scratchpad>/codex-log.txt 2>&1      # or --base <branch> / --commit <sha>
```

Use `run_in_background: true`; runs take minutes. On failure, `tail -n 30` the log.

## 4. Triage

Read `$OUT`. Give **every** Codex point a verdict, checked against the actual code or facts:

- **수용**: verified real. Say what you checked.
- **반박**: wrong or doesn't apply. Give the evidence.
- **보류**: needs the user's call or info you lack.

Done when every point has a verdict with evidence. Report as a table (point / your verdict / evidence), then the fixes you propose. Make no edits until the user picks.
