<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/logo-dark.png">
    <img src="assets/logo.png" width="220" alt="Flowcode, the pair programming tool">
  </picture>
</p>

<h1 align="center">Flowcode</h1>

A skill for AI coding agents that keeps you reading the code instead of waiting for it.

When an agent dumps ten files in one shot, you stop being a programmer and become a spectator. You lose the mental model of your own codebase, you can't debug it later, and you spend the gaps between prompts drifting. flowcode trades a slower start for a developer who understands every line and stays in flow.

## How it works

- The agent writes **one unit at a time**, a function, a class, a small module, in the order a human would build it: types, then core logic, then side effects, then wiring.
- Each unit is proposed as a diff you accept, reject, or tweak.
- Hidden somewhere in each unit is a **4-character hex flag** in a comment. You report it back. No flag, no next unit.
- Every hand-off comes with a real question about the approach, so you're reviewing, not just hunting for hex.

Flags are removed once a unit is accepted, so nothing lingers in your codebase.

## Install

**Claude Code** personal:

```
cp -r skills/flowcode ~/.claude/skills/flowcode
```

Per-project:

```
cp -r skills/flowcode .claude/skills/flowcode
```

Other agents (Codex, Cursor, etc.): point them at `skills/flowcode/SKILL.md` however they load instructions.

Works best with per-edit approval turned on. Auto-accept still works, but then the flag is the only thing standing between you and spectating.

## License

MIT