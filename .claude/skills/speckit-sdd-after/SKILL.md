---
name: "speckit-sdd-after"
description: "Post-stage gate for spec-driven development: review whether the stage's output aligns with what was asked, write a report, then commit and push."
argument-hint: "Stage name (constitution, specify, clarify, checklist, plan, tasks, analyze, implement, converge)"
compatibility: "Requires spec-kit project structure with .specify/ directory"
metadata:
  author: "project"
  source: "extension hook sdd-gates (after_*)"
user-invocable: true
disable-model-invocation: false
---

# SDD After-Stage Gate

Runs after a Spec Kit stage. Purpose: verify that what the stage produced actually matches what
the user asked for and what upstream artifacts require. Fix nothing silently: report gaps.

## Determine the stage

The stage is given in the hook's `Prompt:` line (`Stage: <name> (after)`) or in the user's
arguments. If neither is available, use the Spec Kit command that just finished. If it is still
unclear, ask the user once.

## 1. Alignment review

Establish "what was asked": the user's original input to this stage (feature description,
clarification answers, constitution input), the stage's own instructions, and the upstream
artifacts (constitution, then spec, then plan, then tasks as applicable).

Compare it to what was produced (`git diff` and the new/changed files) and record:

1. **Request coverage**: every element of the user's request mapped to where it is satisfied;
   list anything missing or only partly addressed.
2. **Scope fidelity**: anything produced that was NOT asked for (gold plating, invented
   requirements, unrequested files); anything the user explicitly constrained that was ignored.
3. **Upstream alignment**: traceability to the previous stage (spec to constitution, plan to
   spec, tasks to plan and spec, code to tasks). List orphan items and uncovered requirements.
4. **Constitution compliance**: check each principle; list violations.
5. **Stage quality**: stage-specific checks, e.g. specify: no implementation details, testable
   requirements, no stray `[NEEDS CLARIFICATION]`; plan: constitution check and unknowns
   resolved; tasks: dependency-ordered, tests before implementation, every task traceable;
   implement: tests pass and tasks marked done; constitution: no unexplained placeholders.
6. **Verdict**: ALIGNED, ALIGNED WITH GAPS (list them with suggested fixes), or MISALIGNED
   (list what must be redone).

## 2. Write the report

Write `.specify/reviews/<UTC YYYYMMDD-HHMMSS>-after-<stage>.md` with the sections above, the
stage, the date, and the branch/commit. Cite files and requirement IDs.

## 3. Commit and push

Use the "Commit and push procedure" in `.claude/skills/speckit-sdd-before/SKILL.md` (same rules:
screen for secrets, never force push, never `--no-verify`, stop and report on failure), with
commit message `chore(sdd): after <stage> - alignment review and stage output`.

## 4. Report to the user

Give: stage, verdict, the top gaps (max 5) with suggested next step, the report path, and the
commit hash and push result. If the verdict is MISALIGNED, say so plainly and ask whether to
redo the stage instead of moving on.
