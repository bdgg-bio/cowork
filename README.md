# cowork

Process-automation apps for Branko's botiss workflow.

## daily-desk

`daily-desk/index.html` — **botiss Daily Desk**, published as an Artifact at
https://claude.ai/code/artifact/1a587a40-3877-4004-807b-520c9de19de1

The daily status check-in, as a board you operate instead of a report you retype.
Four tabs:

| Tab | What it does |
| --- | --- |
| **Targets** | Distributor pace against the quarter target. Loads the Excel export (or pasted cells), computes every gap, and charts booked-minus-target as diverging bars, worst at the top. |
| **Approvals** | Orders that arrived by email and are waiting on an "ok" to customer service, with days waiting counted from the arrival date. |
| **Events** | Every BRD event sorted soonest-first, each with the same five readiness lines. |
| **Report** | The one-page check-in in its fixed shape, generated from the board. Copy or download as text. |

### Rules the app encodes

These come from the `botiss-daily-status-check` skill and are implemented in code
so the board and the report can never disagree:

- `gap = Q3 target − booked`. Positive is behind pace, negative is a surplus.
- A distributor with a **€0 target and €0 booked** is "no active target this year":
  its own group at the bottom, never interleaved into the alphabetical list.
- The main list sorts **alphabetically by country**. "Needs attention" ranks only
  the behind-pace ones, by **absolute euro gap** — never by percentage, and never
  including a surplus.
- A **`?` in a distributor name** means the name is unconfirmed in the source sheet.
  It is carried over exactly as written and flagged, never resolved by guessing.
- Every event always gets the **same five readiness lines in the same order**, even
  when all five read "no update yet".
- Nothing is ever guessed. An unreported status is "no update yet", and a day with
  no email input says so in the report rather than implying the approvals list is
  complete.
- The event brand must read **"biomaterials & REGENERATION DAYS"**. An event name
  carrying "DAY" raises a warning with a one-click fix.
- Attendance certificates are the open-name editable PDF, signed by the **BRD
  president** — never the speaker. Stated on the item itself.

### Where the data lives

State is kept in the artifact's own shared store (`db` capability) at document
`state/board`, so the board is the same for everyone with the link and survives
reloads and republishes. `localStorage` is the fallback when the shared store is
unavailable — the pill in the header says which one is in use. Nothing is written
to the shared store until the first real edit, so the example rows the page opens
with never become saved data.

The page opens with rows plainly marked `(example)`. They exist so the board shows
what it does on first look; the banner and the report header both say so, and
"Clear example data" empties it.

### Updating it

Edit `daily-desk/index.html`, then republish to the **same URL** — from a Claude
Code session, pass that artifact URL as `url` to the Artifact tool. Publishing
without the URL creates a separate artifact instead of updating this one.

The page is authored as artifact body content: no `<!DOCTYPE>`, `<html>`, `<head>`
or `<body>` tags — those are added at publish time.
