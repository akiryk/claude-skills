# Claude skills

Personal Claude Code skills, one folder per skill.

| Skill | What it does |
| --- | --- |
| [`ready-to-exit`](ready-to-exit/SKILL.md) | `/ready-to-exit`: checks whether a fresh session could continue the work from the handoff and other docs, and offers to fill any gaps. |

## Install

Claude Code loads personal skills from `~/.claude/skills/<name>/SKILL.md`. Symlink each skill there so edits in this repo take effect immediately:

```sh
mkdir -p ~/.claude/skills
ln -s "$PWD/ready-to-exit" ~/.claude/skills/ready-to-exit
```
