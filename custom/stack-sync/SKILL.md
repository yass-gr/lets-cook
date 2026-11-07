---
name: stack-sync
description: Update the lets-cook stack and its external skills from source. Use when the user says "sync the stack", "update my skills", or when lets-cook flags a stale skill.
---

# Stack sync

The stack repo is `yass-gr/lets-cook` (GitHub). Custom skills live in its
`custom/` tree. External skills are referenced in `manifest.json` and
downloaded at install time, never vendored. Install target is
`~/.agents/skills/`. Source clones live in `~/.cache/lets-cook/db/`.

## Procedure

1. **Refresh the stack repo itself.**

   ```
   cd ~/.cache/lets-cook/db/lets-cook   # clone if missing
   git pull --ff-only
   cp -r $REPO/custom/* ~/.agents/skills/
   ```

   If `~/.cache/lets-cook/db/lets-cook` is a local working copy with
   uncommitted local edits, report first instead of clobbering.

2. **Refresh the manifest's sources.** For each `repo` in
   `manifest.json`, shallow-clone or `git pull` into
   `~/.cache/lets-cook/db/<name>/`, then copy each listed skill directory
   into `~/.agents/skills/<skill>/` (replace existing copy). This updates
   every external skill from source, because sources change on GitHub,
   not in this repo.

3. **Re-sync harness mirrors.** If `~/.claude/skills/` exists and is a
   directory, symlink `~/.agents/skills/<n>` for every stack-managed skill
   name that is missing there. Same for `~/.gemini/skills/`. Do not touch
   non-stack names.

4. **Report** what changed vs last sync: new, updated, removed skills;
   manifest entries whose source moved or vanished.

## Rules

- Only stack-managed names may be replaced. If a skill with the same name
  exists that is NOT in the manifest and NOT in `custom/`, stop and ask
  the user.
- Keep the shallow clones: they are lets-cook's offline playbook source,
  the router reads playbooks from `~/.cache/lets-cook/db/…`, not from the
  skills copies.
- No network during the copy step; clones first, then copy.
