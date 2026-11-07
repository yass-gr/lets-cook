### Opening a PR (agent-agnostic poteto style)

Invoked at the end of every other playbook. No harness-specific tooling is
assumed: everything here works through plain `git` and `gh`.

**Isolation.** Work from the cook worktree claimed in the router's first
moves (`../.cook/<repo>/<short-task>`, branch `cooks/<short-task>`); you
never start a landing playbook from a bare checkout another agent may
touch. Subagents inherit the same worktree. Multiple delegation rounds on
one branch each get their own worktree, or `git fetch && git reset
origin/<branch>` between them. Dirty worktree holding unrelated work:
patch the unrelated part out, fresh worktree, apply the part back.
Snarled state: reset from `main`, redo minimally.

**Commits.** Conventional prefixes (`feat:` `fix:` `chore:` `refactor:`),
one commit per verifiable unit. Commit liberally while working; before
opening the PR, rebase into small ordered commits. Each commit could be a
future PR: landable, ordered to tell the story. Amend when a fix belongs
in the just-made commit; new commit when separable.

**Write clean at draft time.** Apply the `unslop` skill to the PR
description and commit bodies as you write them (no cleanup pass after).

**PRs.**

- Small PRs win: five narrow over one fat. Stack follow-ups rather than
  widening; branch off `main` only for genuinely independent work. For
  stacked slices, keep the stack visible to reviewers (branch base chain),
  no stacking tool is required.
- Run `gh pr view <number>` before referencing its status anywhere.
- Rebase on `main` before substantial stack work.
- No `## Summary` / `## Test plan` boilerplate on small PRs. Commit bodies
  never restate the subject line. The body names what changed, what could
  break, and how it was proven (the `pr` skill owns the longer format when
  the diff deserves it).

**Babysit (native, no harness built-in).** After opening the PR, watch it
yourself until landed or handed over:

1. `gh pr checks <n> --watch` (or `gh run watch` on the PR's run) until
   green, red, or no CI exists. Red: reproduce, fix, push, re-watch.
   Flaky-infra red: re-run once with a concrete link to the failing job in
   your trail, then treat the pattern as a real bug if it repeats.
2. Review comments (human or bot): assess **each on its merits with a
   concrete reason**. Bots like security scanners catch real bugs and also
   file non-issues; triage every finding as fix / dismiss / ask. Dismiss
   noise with evidence, never silently. Push back when feedback drifts
   from the task's intent; agree fast when it doesn't.
3. Never watch-as-deadweight: while waiting on CI, do the next unit.

**Landing (the only place automation differs per repo).** A repo counts
as *own* when the origin owner equals your authenticated `gh` login, or a
one-line per-repo override says so (`gh repo view -R origin-owner/name`
when unsure; never guess at credentials).

- **Own repo:** required gates are the `code-review` skill (standards +
  spec) and green CI when CI exists. Both pass →
  `gh pr merge <n> --rebase --delete-branch`. Rebase-merge keeps the unit
  trail linear; the branch dies automatically. After merge, verify main
  picked the change and report landed SHA + PR URL.
- **Shared/external repo:** full stop after review notes. Post the review
  as PR comments or reply to the thread; the humans merge.

**Pause rules (hard).** Stop and get the user's explicit word before any
irreversible write: force-push to a shared branch, deploy, data deletion,
customer messaging, `gh repo delete`, `gh release delete`, `gh gist
delete`. Creating a repo: only on an explicit user request ("ship it",
"put it on GitHub") and only as **private** by default; public needs the
user's word every time.

**Subagent rule.** A subagent that opens a PR runs the review step,
returns the URL plus check status, and does NOT babysit. Babysitting is
the parent's job, every time.
