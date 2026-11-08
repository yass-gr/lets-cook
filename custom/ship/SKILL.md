---
name: ship
description: Push and land finished code work through the lets-cook finish line (unit commits, review gate, PR via gh, native babysit, rebase-merge + branch delete on own repos, stop-at-PR elsewhere, private-by-default repo creation). Use for "push this", "open a PR", "ship it", "put it on GitHub", "land it", or when the user reports an agent silently finished a code change.
---

# Ship

Never leave a finished code change unpushed and silent. Every code task
ends in exactly one of: the finish line, or an explicit reasoned block on
a listed guardrail.

## Finish line (in order)

1. **Isolation.** Confirm the work sits in its own cook worktree
   (`cooks/<short-task>` branch, `../.cook/<repo>/<short-task>`).
   Never ship from a shared main checkout.
2. **Commits.** Rebase into unit-sized ordered conventional commits
   (`feat:`/`fix:`/`chore:`/`refactor:`). Fixup slop gone.
3. **Review gate.** Run the `code-review` skill (standards + spec). Fix
   critical findings before PR; note the rest in the PR.
4. **Push + PR.** `git push -u origin <branch>`, then `gh pr create` with
   the `pr` skill's body (what, why, proof). No `## Summary` boilerplate
   on small PRs. `gh pr view <n>` before referencing status.
5. **Babysit natively (no harness built-in).** `gh pr checks <n> --watch`
   (or `gh run watch`) until green/red/absent-CI. Red: reproduce, fix,
   push, re-watch; flaky-infra red: one evidence-planned re-run, then
   treat repeats as real bugs. Review comments (human or bot): triage
   each as fix / dismiss / ask with a concrete reason; never swallow.
   While waiting on CI, work the next unit.
6. **Land (the only per-repo difference).** A repo is *own* when origin's
   owner equals your authenticated `gh` login (per-repo override file may
   say otherwise; `gh repo view` when unsure).
   - Own: `gh pr merge <n> --rebase --delete-branch`, then verify main
     picked the change. Report: PR URL + landed SHA.
   - Elsewhere: full stop. Post review notes as PR comments and hand the
     human the URL. You never merge on another person's repo.
7. **No remote?** Repo creation is never silent. User says "ship it" or
   "put it on GitHub" on a remote-less project → `gh repo create <name>
   --private` (private by default; public only on explicit word), wire
   the remote, continue the finish line from step 4.

## At-the-end-of-a-task default (when the user did not ask to ship)

Do not go quiet. State the finish-line step the work reached, then ask:
"Open the PR and ship?" — except when the session ran under the lets-cook
router with an autonomous instruction ("run until done", "while I'm
away"): then ship without asking; the router owns the pause rules.

## Hard pauses (nothing below proceeds without the user's word)

Force-push to a shared branch; history rewrites; `gh repo delete`,
`git push --force` on shared branches, `gh release delete`, `gh gist
delete`; deploys, payments, data deletion, customer messages; creating a
public repo. Full detail in
`~/.agents/skills/lets-cook/playbooks/opening-a-pr.md`.

## Subagents

A subagent may open a PR and returns URL + check status, but never
babysits or lands. The parent owns both.
