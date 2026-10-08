---
name: ready-to-exit
description: Check whether the current session can be exited without losing important context, i.e. whether a fresh session could continue the work from the handoff and other docs. Run only when the user invokes /ready-to-exit.
disable-model-invocation: true
---

The user wants to know whether they can exit this session safely.

**The bar:** a fresh Claude Code session, prompted to read the handoff and/or other docs written so far, could continue this work with minimal risk of missing anything important. "Important" means something the next session would plausibly get wrong, redo, or have to ask the user about again. Not every session needs a handoff: a quick question, or work that's finished and fully explained by the repository, can pass without one.

Don't change any files until the user agrees.

## 1. Gather evidence

Don't answer from memory of what you think you saved. Check:

- **This conversation.** Go through it from the start and list what a next session would need: the objective, what was done, decisions and their reasons, approaches tried and rejected, constraints or preferences the user stated, files that matter, test/status results, open issues, and the next step. If the conversation was compacted and you can't see earlier detail, say so in your reply, since you can't fully vouch for that part.
- **Docs written or updated this session.** Read them as they are on disk now, not as you remember writing them. This includes handoff files (often in `handoffs/`), READMEs, RFCs, plans, CLAUDE.md/AGENTS.md and memory files.
- **Repository state** of every repo touched: `git status`, and unpushed commits (`git log @{u}..` if there's an upstream). Uncommitted changes survive an exit, so they only matter if they're half-finished, broken, or not explained in the handoff.
- **Session-only state** that disappears on exit: running background processes or dev servers, in-progress tasks or todos, scheduled loops, content that exists only in the conversation (a pasted error, a plan you drafted, a value the user gave you).

## 2. Find the gaps

For each item from the conversation, decide whether a fresh session would get it from the repository or the docs. It's covered only if it's actually written down (or obvious from the code), with the reason where the reason matters. Note especially:

- Decisions recorded without their *why*, or rejected approaches not recorded at all, which a new session is likely to retry.
- A handoff written earlier in the session that is now stale: later work, decisions or changes of plan that aren't in it.
- User preferences or constraints that should outlive this task. These belong in CLAUDE.md/AGENTS.md or memory rather than a one-off handoff.
- A handoff whose next step is vague, or that points to docs which don't exist or say something different.

Don't flag trivia or things the code already makes obvious. Each gap you raise should name what a next session would get wrong without it.

## 3. Reply

Use exactly one of these forms.

**Nothing important would be lost:**

> Yes, you can exit now.

Add one short line only if useful, e.g. the handoff path to give the next session.

**Some docs should be updated first:**

> You may want to update `<path>` and `<path>` because <reason for each>. Would you like me to do that now?

If there's no handoff yet and one is needed, say so here ("There's no handoff for this work yet; …"). Keep reasons specific: name the missing decision, rejected approach or next step, not "some details".

**The session shouldn't be exited yet:**

> No, <what isn't finished or would be lost>!

Use this when exiting would cause real loss or a broken state: a half-applied change leaving code broken, a running process or task the user is relying on, work the user asked for that isn't done, or something important that exists only in this conversation. Then say what you'd do to fix it (and, if docs also need updating, include that) and ask whether to proceed.

If the conversation was compacted, add one sentence saying which part you couldn't verify.

## 4. If the user agrees

Update existing docs in place, keeping their structure. Put durable preferences in CLAUDE.md/AGENTS.md or memory, not the handoff.

To write a new handoff:

- If the project has `.claude/SMART-ZONE.md`, follow its "Prepare handoff now" section (path from `.claude/lib/handoff-path.sh`, reply format with a `/clear` prompt), skipping the picker-only step.
- Otherwise write it to `handoffs/NN-handoff-<topic>.md` in the project root, numbered one higher than the highest existing file, starting with `# Handoff: <topic>` and the date. Then reply with its path and a one- or two-sentence prompt for the next session: read the handoff plus only the docs it must read first, then continue with its recommended next step.

A handoff should be concise and cover, without repeating itself:

- Current objective
- What has been completed
- Important decisions and why
- Approaches tried and rejected
- Files changed or especially relevant
- Current test/status information
- Unresolved issues
- The exact recommended next step

Prioritize what isn't obvious from reading the repository. Leave out session-management infrastructure (hooks, settings, debug logs) unless the task concerns it.

After updating, re-run steps 1–2 briefly and finish with "Yes, you can exit now." or whatever still remains.
