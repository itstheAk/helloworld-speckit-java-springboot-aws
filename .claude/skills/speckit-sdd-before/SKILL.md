---
name: "speckit-sdd-before"
description: "Pre-stage gate for spec-driven development: thoroughly analyse everything produced so far, write a report, then commit and push."
argument-hint: "Stage name (constitution, specify, clarify, checklist, plan, tasks, analyze, implement, converge)"
compatibility: "Requires spec-kit project structure with .specify/ directory"
metadata:
  author: "project"
  source: "extension hook sdd-gates (before_*)"
user-invocable: true
disable-model-invocation: false
---

# SDD Before-Stage Gate

Runs before a Spec Kit stage. Purpose: make sure the stage starts from a sound, fully understood
base. Read-only with respect to project artifacts except for the report and the commit.

## Determine the stage

The stage is given in the hook's `Prompt:` line (`Stage: <name> (before)`) or in the user's
arguments. If neither is available, use the Spec Kit command about to run. If it is still unclear,
ask the user once.

## 1. Thorough analysis of everything so far

Read, as they exist (skip what does not exist yet, and say so):

- `README.md` and `.specify/memory/constitution.md` (principles, version, governance)
- The feature directory from `.specify/feature.json` (else the newest `specs/NNN-*`): `spec.md`,
  `plan.md`, `research.md`, `data-model.md`, `contracts/`, `quickstart.md`, `tasks.md`,
  `checklists/`
- Prior reports in `.specify/reviews/`
- `git log --oneline -15`, `git status --short`, and the diff of uncommitted work
- Any source, Terraform, Kubernetes, and CI files that already exist

Analyse and record:

1. **State**: which SDD stages are complete, partial, or missing, and what the next stage needs
   as input.
2. **Readiness**: is every input this stage needs present and sufficient? Name gaps.
3. **Consistency**: contradictions or drift between constitution, spec, plan, tasks, and code.
4. **Constitution compliance**: check each principle against current artifacts; list violations.
5. **Risks and open items**: unresolved `[NEEDS CLARIFICATION]`, TODOs, unchecked checklist
   items, failing or missing tests, security concerns (secrets, unpinned dependencies, broad
   permissions).
6. **Recommendation**: PROCEED, PROCEED WITH CAUTION (list conditions), or STOP (list blockers).

## 2. Write the report

Write `.specify/reviews/<UTC YYYYMMDD-HHMMSS>-before-<stage>.md` with the sections above, the
stage, the date, and the current branch/commit. Keep it factual and specific (cite files and
requirement IDs).

## 3. Commit and push

Follow the shared procedure below, with commit message
`chore(sdd): before <stage> - analysis report and checkpoint`.

### Commit and push procedure (shared with speckit-sdd-after)

1. `git status --short`. Review what would be staged. If any file looks like a secret
   (`.env`, keys, credentials, tokens, `*.tfstate`, `*.tfvars` with secrets), do NOT stage it;
   report it and continue without it.
2. `git add` the remaining changes, then commit with the message above, ending with the
   attribution line from the session's system reminder if one is present. If there is nothing to
   commit, skip the commit.
3. `git push`. If the branch has no upstream, use `git push -u origin <branch>`. Never force
   push, never use `--no-verify`, never change branches or rewrite history.
4. If a commit hook or push fails, stop, report the exact error, and leave the working tree as
   is. Do not retry in a loop.

## 4. Report to the user

Give: stage, recommendation (PROCEED / CAUTION / STOP), the top findings (max 5), the report
path, and the commit hash and push result. If the recommendation is STOP, say clearly that the
stage should not continue until the blockers are resolved, and wait for the user's decision
instead of continuing automatically.
