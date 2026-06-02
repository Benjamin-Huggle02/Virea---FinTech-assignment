# Virea — Pay the RSVP

**Virea turns a soft RSVP into a confirmed one: a guest's spot is reserved only when they pay.** No app, no account — just a link. Hosts stop fronting money and chasing friends; guests tap once and they're in.

This repository is the MVP built for a university FinTech assignment. It is a **sandbox demo** — no real money moves — designed to show the core product loop convincingly in a live walkthrough.

> ⚠️ **Demo notice.** Payments are mocked (a button + processing spinner). Nothing is ever charged. This is a concept demonstrator, not a payment product. See [What's real vs mocked](#whats-real-vs-mocked).

---

## The core loop

```
  HOST                    GUEST (phone)              HOST DASHBOARD
  create event  ──link──►  open link                 (live)
                           answer questions
                           Pay €5  ──confirms──►      guest appears
                                                      1 of 12 · €5 collected
                                                      …fills to "full"
```

Five beats, one screen each — that is the whole product.

## Features

- **Host event builder** — title, date, location, capacity, contribution amount, public/private toggle. The form starts blank for the host to fill in.
- **Up to 5 custom guest questions** — short-text or multiple-choice, and each choice question can accept a **single answer or multiple answers**.
- **Public and private links:**
  - *Public* — auto-fills and locks at *"event full"* when capacity is reached.
  - *Private* — the host adds an invite list of names; only a guest whose name matches the list can RSVP.
- **Shareable guest link + QR code** — generated on save, rendered inline (offline-capable, no external service).
- **Mobile-first guest flow** — view event → answer → pay → confirmation receipt. No app, no forced account.
- **Live host dashboard** — confirmed guests, their answers, spots filled vs capacity, total collected. Refreshes every 2 seconds.

## Run it

It's a single self-contained HTML file with **zero build step and zero dependencies**.

```bash
# either just open it:
open index.html            # macOS
xdg-open index.html        # Linux
# or serve it (any static server works):
python3 -m http.server 8000   # then visit http://localhost:8000
```

The top-right switcher (**Host · Dashboard · Guest phone**) lets you move through the three roles in one browser window.

## How it maps the concept to code

The product's one conceptual innovation is **"the spot is real only when paid."** In code, that is enforced in exactly one place: a guest exists in the system *only* after the (mock) payment callback fires and writes a `confirmed` attendance. There is no "maybe" / "interested" state — that absence is the point. Everything the host sees (counts, money, capacity, the "full" gate) is derived from confirmed attendances, so the dashboard can never show a commitment that wasn't paid for.

```
index.html
├─ App state            single source of truth (event, attendances, guest draft)
├─ CreateView()         host event + question builder (single/multi answers),
│                       public/private + invite list, slug + QR generation
├─ GuestView()          phone mockup: form → paying → confirmed → full
│   └─ commitAttendance()   the "payment succeeded" write — the only way a guest enters
├─ DashView()           derived live view (counts, total, capacity, "full")
└─ startPolling()       re-renders the dashboard every 2s (simulates server polling)
```

## What's real vs mocked

| Part | Status | Note |
|---|---|---|
| Event creation & questions | **Real** | Single- and multiple-answer choice questions |
| Public / private + invite list | **Real** | Private events match guest name against the host's list |
| Guest link + QR code | **Real** | QR generated client-side (qrcode-generator, MIT, inlined) |
| Guest answer capture | **Real** | Stored on the attendance record |
| Capacity / "full" logic | **Real** | Derived from confirmed count |
| Live dashboard | **Real** | 2-second polling refresh |
| **Payment** | **Mocked** | Button + ~1.6s spinner → marks spot confirmed. No money, no card, no Stripe. |
| Persistence | **In-memory** | State resets on reload (intentional for a clean demo) |
| Email / accounts / auth | **Skipped** | Out of scope for a sandbox demo |

To make this production-grade you would swap `commitAttendance()` for a real payment provider (e.g. Stripe PaymentIntent + webhook as source of truth), move state to a server + database, and add auth. None of that is needed to demonstrate the concept.

## Tech

Plain HTML + CSS + vanilla JS in one file. The original handover specced Next.js + Prisma + SQLite; for a lightweight demo that stack adds setup and runtime overhead without changing the end-user experience, so this build collapses it to one portable file. The reasoning is documented in [`docs/DECISIONS.md`](docs/DECISIONS.md).

## Topics

`fintech` · `mvp` · `events` · `payments-demo` · `rsvp` · `vanilla-js` · `school-project`

## License

MIT (see `LICENSE`). Bundled QR library: qrcode-generator by Kazuhiko Arase, MIT.
