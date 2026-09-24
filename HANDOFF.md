# Tracker — session handoff brief (supplement to TRACKER-PROJECT.md)

Reconstructed from two session transcripts (6,641 + 3,684 messages, Sep 5–13) after the desktop
app's session list was lost in a reinstall. Both are intact at
`~/.claude/projects/C--Users-fayza-OneDrive-Documents-Claude-code-Spending-tracker/`
(`83617bf1-…` Sep 5–12, `d874dbd6-…` Sep 12–13). Resume those directly if possible.

## Read TRACKER-PROJECT.md first — then this

`TRACKER-PROJECT.md` is a genuinely good handoff doc and still describes the architecture
correctly: one `index.html` (no framework, no build step, no backend), `sw.js`, `test.js` run with
`node test.js`, deploy by bumping the `slip-build` meta tag and pushing to GitHub Pages.

**But it has gone stale in two specific ways:**

| It says | Actually |
|---|---|
| Build `2026-09-01-25` | Several builds on — head is `663619e` (Sep 13) |
| 309 tests | **395 tests** |
| "Everything is in ZAR" | No longer true — see below |

Treat it as accurate on *how the app is built and deployed*, and this file as accurate on *what
changed after Sep 1*.

## What changed since TRACKER-PROJECT.md was written

### Multi-currency (the big one — and it was partly reverted)

The currency system was made configurable in staged commits, then **deliberately rolled back and
selectively re-applied**. This is the single most confusing thing in the git history, so:

- Sep 12 built: EUR as a 5th currency, per-month main currency, savings pots with their own
  currency, a withdrawal conversion toggle, a "default currency" setting.
- The user then decided to **fork the whole feature set to a separate share repo** and revert their
  own (`2d8c2a1 Revert today's currency work back to yesterday's state`).
- Then re-applied a chosen subset (`71aa608 Drop EUR, keep the per-month/pot currency and savings work`).

**Net state of the personal repo today:** 4 currencies (R / £ / $ / ₵) — **no EUR**. Per-month main
currency, pots with independent currencies, and the withdrawal toggle are all **in**. There is
**no** "default currency" setting, so allowance day still defaults to the 25th.

Past months are frozen forever once their currency is set.

### Other changes

- **Investments moved to its own bottom-tab sheet** (`c61617f`) — it isn't day-to-day, so it's out
  of the main flow. Holdings are tracked mainly in **USD**, since that's what's actually bought;
  rands only appear for conversion. Funded via Wise, bought on EasyEquities. Prices via **Alpha
  Vantage**, key `VNQHJDTRZ6JJDAAC`.
- **Rent, water and similar are hidden from the "this week" section** — they aren't weekly expenses.
- A **first-time user guide** was written into the README.

### The savings-goal bug (Sep 13) — understand this before touching savings

Reported as "I took out 3500 from my savings to buy something but it didn't record it and now I'm
very in the negative." It was not data loss. The goal was £3,500; saving toward it correctly stopped
reserving that amount from daily spending, but **withdrawing made the goal "unmet" again, so the app
immediately re-reserved the same £3,500** to protect it. The withdrawal therefore freed zero
spendable money, and the subsequent purchase came straight out of protected funds.

Fix (`663619e`): a deliberate withdrawal now permanently lowers how much is protected toward that
cycle's goal instead of snapping back. The same fix was applied to the share repo.

## The share fork

`fayzalad/Spending-Tracker-Share` — a separate repo and a separate working clone at
`Claude code/Spending tracker (Share)`, with its own `HANDOFF.md`. Independent history; commits and
pushes in one never touch the other. See that folder for what was customized.

## Session texture worth knowing

- The Sep 5 session opened by installing a batch of third-party skill packs (emilkowalski, ponytail,
  OmniRoute, graphify, addyosmani/agent-skills). **OmniRoute was reviewed and removed** — the user
  had understood it to give "almost unlimited usage by connecting to other AI providers," and after
  explanation said "forget it then." It was noted to memory in case it's wanted later. Don't
  re-add it unprompted.
- The user asked about **iOS liquid glass** styling on Sep 11 and then dropped it ("just forget it
  then"). Not a live request.
- Testing is done on the actual phone, and the user reports back visually rather than from logs.

## Where it stopped

Sep 13 22:29 — the savings-goal fix was applied to the personal repo, then propagated to the share
repo, and confirmed working ("its working now").

Sep 24–25 — Antigravity revamp completed:
- Installed 31 agent skills from `rmyndharis/antigravity-skills` to `.agents/skills` and updated `skills-lock.json`.
- Hardened input sanitization with `escHtml` to prevent XSS.
- Memoized search string indexing (`getHay()`), removing keystroke lag.
- Added Apple-grade physical press transitions, haptic taps, and high-contrast dual signaling on budget progress bars.
- Added `#monthInsightsCard` to the Month sheet for savings rate, living vs bills breakdown, and daily burn metrics.
- Hardened `sw.js` (cache `slip-v4`) against external API document fallback leaks.
- Build tag bumped to `2026-09-25-1`.
- Expanded test suite to **433 checks**, all passing (`node test.js`).
