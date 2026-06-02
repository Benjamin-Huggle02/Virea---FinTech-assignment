# .claude/ — Claude Code orchestration

This directory holds agent configuration for Claude Code, the coding agent used to build Virea.

## How agents are orchestrated on this project

A single agent works the repo against the contract in the root [`CLAUDE.md`](../CLAUDE.md). The workflow is phase-based and the agent stops for a human checkpoint after each phase:

1. **Skeleton** — single-file scaffold, state model, three view stubs.
2. **Host side** — event + question builder, slug + QR.
3. **Guest flow** — the mobile pay→confirm path (highest priority).
4. **Live & polish** — dashboard polling, capacity gate, styling pass.
5. **Feature pass** — multi-answer questions, private invite list, logo, blank-start form.

After each phase the agent reports a one-paragraph summary and any decisions made (logged in [`../docs/DECISIONS.md`](../docs/DECISIONS.md)), then waits.

## Guardrails the agent enforces

- Never wires real money or card handling.
- Keeps `index.html` dependency-free (inlines anything it needs).
- Treats the guest mobile flow and live dashboard as load-bearing; verifies the full loop after every change.
- Declines scope-creep from the SKIP list instead of building it.

## Verifying a change

Open `index.html`, then walk: Host → create/save → Guest phone → answer → Pay → confirmation → Dashboard shows the guest, count, and total → repeat to capacity → "event full." For a private event, confirm a non-listed name is refused and a listed name is accepted.
