---
name: skill-lab
description: "Install third-party skills into an isolated project repo instead of the shared profile. Use when a skill must not leak into other sessions, or when a vendored skill gets quarantined."
metadata:
  hermes:
    tags: [skills, isolation, project-local, vendoring]
---

# Skill Lab

Skill lives in `<repo>/.hermes/skills/`, not in `$HERMES_HOME/skills/`. Only sessions whose
cwd is inside that repo see them. Everything else keeps the profile's skills untouched.

## Setup once per machine

```bash
mkdir -p <repo>/.hermes/skills && cd <repo> && git init -b main
hermes skills trust "<repo>"          # native path, forward slashes
hermes config get skills.trusted_project_dirs
```

## Install

Vendor the files (git can then track/diff them):

```bash
git clone --depth 1 <repo-url> "$TEMP/src"
cp -r "$TEMP/src/<skill>" <repo>/.hermes/skills/<skill>
```

`hermes skills install <id>` is NOT used — it always writes to `$HERMES_HOME/skills`
(global for the profile), which is exactly the collision being avoided.

Verify from inside the repo:

```bash
env -u TERMINAL_CWD hermes skills list | grep project
```

## Pitfalls

- `TERMINAL_CWD` overrides the shell cwd, so `find_project_root()` returns `None`.
  Project skills only load when the **session** cwd is the repo — a `cd` in `terminal`
  is not enough.
- A project skill whose files trip `tools.skills_guard` (verdict `dangerous`) is
  quarantined silently: absent from `skills list`/`skill_view`. Check before trusting a
  vendor copy:
  `agent.skill_utils.is_quarantined_project_skill(<dir>/SKILL.md)` — returns the reason
  string or `False`. Do not reach for `--force` on the project path.
- Moving the session itself: `session.mutate operation=workspace payload={cwd}` (the same
  call the desktop's *Move to project* makes) is refused with `session_busy` **for the
  whole duration of a turn**. Schedule it between turns; it cannot be done from the tool
  call that wants it.
- Drop `__pycache__`, `assets/`, `examples/` before committing a 60 MB skill repo — check
  `grep -c "examples/" SKILL.md` first to see whether they are referenced.
