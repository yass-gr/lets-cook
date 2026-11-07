# lets-cook 🔪🥘

Yassine's universal agent stack: one set of agent skills that works the
same in OpenCode, Claude Code, Codex, Gemini CLI, or any harness that reads
`~/.agents/skills/` or has a skills mirror.

Philosophy, decided in a grilled design session:

- **On-demand rigor.** Nothing auto-triggers. `/lets-cook` ("lets cook")
  routes real work into rigorous playbooks.
- **Code-first.** No specs, no ticket ceremony. The best spec is code.
- **Pragmatic TDD.** Red-green for bug fixes with a cheap test path. No
  test theater elsewhere, but proof before done, always.
- **Fast cheap onboarding.** Every project gets a one-page **hub skill**
  any agent loads instead of re-reading the codebase.
- **Single review gate.** standards + spec review before shipping, no
  multi-model panels.
- **Speed wins conflicts.** Where stacks disagreed, the fastest solid tool
  won.

## Install

```bash
git clone https://github.com/yass-gr/lets-cook
cd lets-cook && ./bootstrap
```

The bootstrap:

1. Installs `custom/` skills (lets-cook router, project-onboard,
   stack-sync) into `~/.agents/skills/`.
2. Reads `external/manifest.json`, shallow-clones the sources
   (mattpocock/skills, poteto/plugins, obra/superpowers) into
   `~/.cache/lets-cook/db/`, and copies each referenced skill into
   `~/.agents/skills/`. Externals are never vendored in this repo.
3. Mirrors stack-managed skills into `~/.claude/skills` and
   `~/.gemini/skills` when those harnesses exist.

## Use

In any project, with any agent:

| You say | What happens |
|---|---|
| `lets cook` / `/lets-cook` | The router takes the task: playbook match, principles, proof |
| `onboard this project` | `project-onboard` writes the project's hub skill + AGENTS.md stub |
| `sync the stack` | `stack-sync` updates custom + external skills from source |

First time in a project: run `onboard` once. After that agents read the
hub, not your whole repo.

## Layout

```
custom/                 skills authored in this repo
  lets-cook/            the router (forked from poteto-mode, unplugged)
  project-onboard/      generates per-project hub skills
  stack-sync/           updates the stack and externals from source
external/
  manifest.json         refs to every external skill + exclusion reasons
bootstrap               installer (python3, no deps)
```

## Choosing table (what we took and why)

See `external/manifest.json` → `excluded` for every skill from the three
source stacks we left out, with the reason:

- pstack: `architect`, `how`, `why`, `tdd`, `figure-it-out`,
  `show-me-your-work`, `unslop`, `reflect`, `typescript-best-practices`,
  plus all `principle-*` leaves. `poteto-mode` was forked into
  `custom/lets-cook` (Cursor-specifics removed, planning already absent).
- mattpocock: `code-review` (the only review gate), `diagnosing-bugs`
  (fastest debug loop), `prototype`, `research`, `retro`, `handoff`,
  `writing-for-agents`, session tools, writing stack, TS tooling.
  Full spec chain (`to-spec`/`to-tickets`/`wayfinder`) excluded by design.
- superpowers: `verification-before-completion`, `using-git-worktrees`,
  `subagent-driven-development`, `dispatching-parallel-agents`,
  `finishing-a-development-branch`. Its methodology core (brainstorm →
  plans → strict TDD) excluded: conflicts with code-first + pragmatic TDD.

## Maintenance

- Skills drift when their sources move. Run "sync the stack" anytime.
- Tutoring the router after real use: `reflect` on a session, then edit
  `custom/lets-cook/SKILL.md` in its own commit.
- Git guardrails live as hard rules inside the router (portable, zero
  install). Add real hooks per harness later if wanted.

MIT licensed. Credit to mattpocock/skills, poteto/plugins (pstack), and
obra/superpowers, the three stacks this one is cooked from.
