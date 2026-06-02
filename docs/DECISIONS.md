# Decisions log

Short record of the choices that shaped this build, for the report and the video's "why" sections.

## Stack: one HTML file instead of Next.js + Prisma + SQLite
The handover specced a full Next.js + Prisma stack. For a lightweight demo that stack adds setup and runtime overhead (migrations, dev server, port issues) without changing the end-user experience. Collapsing to one dependency-free `index.html` makes the demo portable and crash-proof. The conceptual mapping (paid = confirmed) is identical either way.

## Payment: mocked, not Stripe test mode
Mock payment (button + spinner → confirm) was chosen over Stripe test mode. It demos identically in a recording, needs zero external setup or keys, and keeps the file self-contained. The "(demo payment)" label keeps it honest. Stripe test mode is documented as the first upgrade if a real card UI is wanted.

## Single window, three roles
A static file can't sync two physical devices without a server. Instead the host, dashboard, and guest phone are three views over one shared in-memory state, so "the guest pays → the dashboard ticks up" happens live on one screen. This demos *better* on video than juggling a real second phone.

## QR library inlined
The external QR CDN was blocked/unreliable, and a live demo shouldn't depend on the network. The MIT-licensed qrcode-generator library is inlined so the file works fully offline.

## In-memory state, resets on reload
No persistence by design — every reload gives a clean event for a fresh take. A production build would move state to a server + database.

## Feature pass (later addition)
After the core loop worked, a small set of features was added to make the demo richer without breaking the "one file, no real money" rules:
- **Single vs multiple answers** on choice questions, so the question builder feels complete.
- **Private invite list** — private events match the guest's typed name against a host-supplied list, demonstrating the public/private distinction concretely rather than as a label.
- **Blank-start create form** — placeholders instead of pre-filled values, so the host visibly builds the event during the walkthrough.
- **Logo in the top bar** for a more finished, branded look on camera.
