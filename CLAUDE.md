# CLAUDE.md — Agent instructions for Virea

This file orients any AI coding agent (Claude Code, Cursor, Copilot) working in this repo.

## What this project is

Virea is a **school FinTech MVP**: a pay-to-confirm RSVP tool for informal events. The deliverable is a convincing **live demo**, not a production payment system. Optimise every decision for "looks real and smooth in a short screen recording," not for scale or security hardening.

## Hard rules

- **No real money, ever.** Payments are mocked. Do not integrate a live payment processor or handle real card data.
- **No regulatory / KYC / auth complexity.** It's a sandbox demo. Simpler is correct here. Do not add compliance scaffolding "to be safe."
- **Keep it one portable file.** `index.html` is self-contained with no build step and no runtime network calls. Preserve that — it's what makes the demo robust. If you add a dependency, inline it.
- **The grade rests on the guest mobile flow and the live dashboard.** Protect those two above all else.

## Architecture (one file: `index.html`)

- A single `App` object holds all state (event, attendances, guest draft). No framework.
- Three views share that state: `CreateView` (host), `GuestView` (phone mockup), `DashView` (live dashboard).
- The conceptual core: a guest enters the system **only** through `commitAttendance()`, which runs after the mock payment resolves. Everything the host sees is derived from confirmed attendances. Do not add an unpaid "interested" state — its absence is the product thesis.
- `startPolling()` re-renders the dashboard every 2s to simulate server polling.

## Feature notes

- Questions support `type: 'text' | 'choice'`, and choice questions carry a `multi` flag (single vs multiple selectable answers).
- Events carry a `visibility` of `public` or `private`. Private events hold a `guestList` of names; a guest may only RSVP if their entered name matches an entry. Public events auto-fill and lock at capacity.
- The create form starts blank (placeholders only) so the host fills it in live during a demo.

## Style

Purple accent `#7C3AED`, rounded cards, generous spacing, the Virea logo in the top bar. Sentence-case, plain-verb copy. Keep the "(demo payment)" honesty label on the pay step.

## When you make changes

- Small, single-purpose commits with clear messages.
- After any change, open `index.html` and verify the full loop: create → guest pays → dashboard updates → fills to "full." For private events, verify the name-match gate.
- If a request would pull in scope from the SKIP list (real payments, webhooks, accounts, multi-currency, native apps), note it and decline rather than building it.

## Out of scope (do not build)

Real payments/refunds/webhooks · accounts/auth · email · multi-currency · native apps · any regulatory feature · the deck's business-model layers (ads, recommendations, treasury).
