---
name: project-onboard
description: Generate (or refresh) a project's hub skill so any agent, any harness, plugs in fast and cheap. Use when a project has no hub, after a big refactor, or when the user says "onboard this project".
---

# Project onboard

Goal: one lean, agent-facing map per project. The hub replaces re-reading
the codebase at every session start. It records truth from code, never
intent from docs.

## When

- No hub exists for this project and real work is coming.
- The architecture changed and the hub drifted (agents flag stale lines).
- The user asks for a refresh.

## Deliverables (two files, never more)

1. **The hub skill** at `<project>/.agents/skills/<project-name>/SKILL.md`.
   One file, aim for 80 lines or fewer. Sections, in this order:

   - Purpose in one sentence.
   - Map: main directories with one line each, what lives where, where NOT
     to look.
   - Data shapes: the named structures the codebase thinks in (models,
     schemas, stores). Name them, say which file owns each.
   - Commands: install, build, run, test, lint, deploy exactly as the repo
     expects them. From config files, not from memory.
   - Workflow pin: how this owner likes work done here (branch style,
     commit style, anything from AGENTS.md conventions).
   - Hazards: env files, migrations, generated code, deployment gotchas.
   - Maintenance note: how hub lines get updated (see "Update policy").

2. **The memory-file stub** at `<project>/AGENTS.md` (or `CLAUDE.md` when
   only that is read; write both if both conventions are used but keep each
   to a pointer). Body:

   - One line: "Before any work, load `<project-name>` from
     ./agents/skills/."
   - One line: "Finished a task that changed code? Do not go silent: run
     the ship skill's finish line (commit → review gate → push → PR →
     babysit → land), or, if you cannot reach it, ask 'open the PR and
     ship?'."
   - Up to ten lines of fragments that must be visible even before the hub
     loads: language and package manager, run command, test command. If
     the lets-cook stack is installed, the ship rule may instead point at
     the installed `ship` skill and the hub keeps its stub to five lines.

Merging: if the project already has AGENTS.md/CLAUDE.md content, keep it
and append the pointer. Never overwrite human-written docs.

## Rules

- Read-only exploration of the code. Write only the two files above (and
  the directories they live in).
- Hub lines must be verifiable in code at the time of writing. If docs and
  code disagree, record the code.
- No refactors, no README rewrites, no code comments in passing.
- Big repo, low token budget: explore entry points and configs first,
  then stop once every hub section has at least one verified line.

## Update policy (this is the self-updating part)

Agents working with lets-cook cook'd projects act as maintenance leave:

- At session start, after loading the hub, spot-check only the lines the
  current task touches (commands, maps, shapes). If a line is stale, fix
  that line in the hub file in the same commit as the change that broke
  it.
- Never rescan the whole repo to check the hub.
- A big refactor the user calls out → full re-onboard.
