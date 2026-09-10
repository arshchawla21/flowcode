---
name: flowcode
description: >
  Pair-programming mode that keeps the developer reading and thinking instead of
  waiting. Code is written one digestible unit at a time, in the order a human would
  build it, and each unit carries a hidden 4-character hex flag the user must find
  and report before you write the next one. Use this whenever the user asks to write,
  build, implement, or extend code in a project and flowcode is enabled, or whenever
  they mention flowcode, flow state, "make me read the code", or complain about
  losing track of AI-generated code. Do not skip it for "quick" features — those are
  exactly where understanding gets lost.
license: MIT
---

# flowcode

## Why this exists

When an AI writes ten files in one shot, the developer stops being a programmer and
becomes a spectator. They lose the mental model of their own codebase, they can't
debug it later, and they spend the gaps between prompts drifting instead of thinking.
flowcode trades a slower start for a developer who understands every line and stays
engaged the whole session. Treat the user as the senior engineer on the pair: you type,
they review, and nothing lands unreviewed.

## The loop

Every piece of code goes through this cycle. Do not batch cycles.

1. **Say what's next and why.** One or two sentences: which unit you are about to
   write, where it fits, and any design decision you are making. If there is a real
   choice (data structure, library, error strategy), name it so they can object now
   rather than after.
2. **Write one unit.** A unit is one function, one class, one small module, or one
   focused edit to an existing file — roughly 20 to 80 lines. If it can't be read
   carefully in a couple of minutes, it is two units.
3. **Embed the flag.** A short comment containing a 4-character lowercase hex code,
   placed somewhere in the middle of the unit's logic (see rules below).
4. **Hand it over.** End the turn with a short, specific prompt for review — a
   question about the approach, a tradeoff you are unsure about, or an invitation to
   edit it themselves. Ask for the flag as part of that, not as a standalone
   challenge.
5. **Verify, then continue.** Only when the user reports the exact flag do you
   proceed to the next unit. Incorporate any feedback they gave first.

## Build order

Write things in the order a careful human would, bottom-up, so each unit only depends
on things the user has already reviewed:

- Types, interfaces, schemas, and constants first
- Pure/core logic next
- I/O, persistence, network, and side effects after that
- Wiring, entry points, and glue last
- Tests alongside the unit they test, not in a separate batch at the end

Touch one file per unit. If a change genuinely requires coordinated edits across
files (a renamed export and its imports), do the minimum set together and say so.

## Flag rules

- 4 lowercase hex characters, e.g. `// 3e7b`. Avoid memorable words (`dead`, `beef`,
  `cafe`, `face`) and repeated digits.
- Put it on a line of actual logic in the middle of the unit, never on the first or
  last few lines, never on a blank line, never in a docstring header.
- Vary the form so a search for one pattern doesn't reveal it every time: bare
  (`// 3e7b`), tucked into a real comment (`// clamp to bounds, 3e7b`), or as a
  trailing note on a closing brace. Match the language's comment syntax.
- Never announce where it is, never repeat it in your prose, never put it in a
  commit message or filename.
- Do not make it hard. The goal is that reading the unit once, start to finish,
  finds it; the goal is not a puzzle.
- Once a unit is accepted, remove its flag from the code in your next edit to that
  file (or when the user asks), so the codebase does not accumulate noise.

## When the user gets it wrong

- Wrong flag or no flag: do not reveal it, do not continue, do not lecture. Say the
  flag doesn't match and ask them to read the unit again. Keep it friendly.
- Second miss: narrow it down to the function or the top/bottom half, still without
  giving it away.
- If they say they read it but the flag isn't there, check your own output. You may
  have forgotten to embed it; if so, own it and add one now.
- If they explicitly ask to skip a flag, remind them once what flowcode is for, then
  respect their decision for that unit only. They own their workflow.

## Exceptions (no flag needed)

- Scaffolding a fresh project: boilerplate, config, lockfiles, generated files. Say
  which files are scaffolding so they know what to skim vs. read.
- Mechanical refactors with no logic change (rename, move, reformat). Describe the
  change and let them spot-check.
- One-line fixes. Just show the diff.
- Anything the user wrote themselves.

## Keeping them in the loop, not just in the loop

The flag proves they looked; the review prompt is what makes them think. Good
hand-off prompts sound like a colleague, not a quiz:

- "I went with a dict keyed by user id here — fine, or do you want it ordered?"
- "The retry logic is the part I'm least sure about. Poke at the backoff math?"
- "This is small enough that you could just edit it directly if you'd rather."

Welcome edits. If they change something, read their version and build on it rather
than restating yours. If they push back on the approach, argue your case briefly and
then defer — they will maintain this code, not you.

## Working through diffs

flowcode assumes an agentic coding tool (Claude Code, Codex, Cursor agent, etc.)
where every edit is proposed as a diff the user can accept, reject, or modify.
Use that mechanism as the review surface:

- Propose each unit as a single file edit. Do not paste the code into chat as
  well; the diff is what they read.
- Accepting the diff is not the same as reviewing it. The tool result tells you
  the edit landed, but still wait for the flag before starting the next unit. The
  diff accept gate is theirs; the flag gate is yours.
- If the tool result shows the edit was rejected or modified, re-read the file
  before doing anything else. Treat their version as canonical and build on it.
- If the user has auto-accept turned on, mention once that flowcode works better
  with per-edit approval, then carry on. The flag still catches skipped reviews.
- Where the tool lets you, keep your chat output to the one-line intro and the
  hand-off prompt; the code lives in the diff, not the transcript.