# cowork

Process-automation apps for Branko's botiss workflow.

## daily-desk

`daily-desk/index.html` — **botiss Daily Desk**, published as an Artifact at
https://claude.ai/code/artifact/1a587a40-3877-4004-807b-520c9de19de1

The daily status check-in, as a board you operate instead of a report you retype.
Five tabs:

| Tab | What it does |
| --- | --- |
| **Targets** | The 15 distributors from `2026_plan.xlsx`. Pace against the Q3 target as diverging bars (worst first), a ranked shortfall list, and a month-by-month heatmap of where the year landed. |
| **Approvals** | Orders that arrived by email and are waiting on an "ok" to customer service, days waiting counted from arrival. Starts empty — the workbook carries revenue and events, not the inbox. |
| **Events** | The 14 events from the workbook's `events` sheet, soonest first, each with the same five readiness lines, plus the September VIP room allocation. |
| **Checklist** | The BRD event checklist as a live per-event form, with progress split by who owes what and two chase notes generated from the gaps. |
| **Report** | The one-page check-in in its fixed shape, generated from the board. Copy or download as text. |

### Where the numbers come from

Seeded from `2026_plan.xlsx` as supplied (loaded 10 September 2026). The extraction
was reconciled against the sheet's own formulas before being embedded — `per Q` =
full-year target ÷ 4, `Q3 target` = that × 3, `missing` = Q3 target − booked, and the
monthly cells sum exactly to the "2026" column. Totals match the sheet's own total
row to the cent (Q3 target €1,062,983.19 · booked €1,014,404.80 · €48,578.39 behind).

Two findings the sheet itself surfaces, both flagged in the app rather than resolved:

- **Two disagreeing target sets.** The `2026 target` column that drives Q3 pace is
  last year × 1.05 for every distributor *except Jordan*, which is set to its agreed
  plan (€105,000) instead of the escalated €131,260. The table further down the sheet
  holds the agreed 2026/2027 plan, and for 8 distributors the two differ by more
  than 5% (UAE is the widest: €286,983 vs a €200,000 plan). The board follows the
  pace column, as the check-in does, and shows both.
- **September VIP rooms.** The events sheet notes "VIP event mix/24 rooms" while the
  per-country split below it adds up to 26.

The `5% budget` column on the events sheet is exactly 5% of each distributor's 2026
target, so the app recomputes it when a target is edited.

### Rules the app encodes

From the `botiss-daily-status-check` skill and the BRD event checklist, in code so the
board and the report can never disagree:

- `gap = Q3 target − booked`. Positive is behind pace, negative a surplus.
- A distributor with a **€0 target and €0 booked** is "no active target this year":
  its own group at the bottom, never interleaved. (Egypt, Iran, Oman.)
- The main list sorts **alphabetically by country**. "Needs attention" ranks only the
  behind-pace ones by **absolute euro gap** — never by percentage, never a surplus.
- A **`?` in a distributor name** means the name is unconfirmed in the sheet. It is
  carried over exactly and flagged, never resolved by guessing. (KSA carries a `?` and
  names no distributor at all; Syria's revenues row carries one where its events row
  does not.)
- Every event always gets the **same five readiness lines in the same order**, even
  when all five read "no update yet".
- Nothing is guessed. An unreported status is "no update yet", a day with no email
  input says so in the report, and a sheet cell that gives no day (AEEDC) says that.
- The event brand must read **"biomaterials & REGENERATION DAYS"**. A name carrying
  "DAY" raises a warning with a one-click fix.
- Attendance certificates are the open-name editable PDF, signed by the **BRD
  president** — never the speaker.
- The **botissCAMPUS tag cap of four** is enforced: the rest disable at four.

### The checklist tab

Every field of the BRD event checklist, per event, stored per event. The source
document colours its fields — partners fill the green ones, speakers the red — so each
row carries a Partner / Speaker / botiss owner chip and progress is reported per owner.
Sections: the event, marketing, timetable (the fixed 09:00–17:30 shape with four
fillable teaching slots), programme (repeatable speaker blocks — lecture title,
speaker, abstract, CV, hands-on title and abstract), botissCAMPUS tags, botiss products
with per-participant quantities, instruments, animal products, medical consumables, and
the hands-on format notes.

Two generated outputs turn the gaps into messages: **Copy what the partner still owes**
and **Copy what the speaker still owes** list only that side's unfilled fields, with the
document's own guidance under each. **Download the filled checklist** writes the whole
thing out as text.

### Where the data lives

State is kept in the artifact's own shared store (`db` capability) at document
`state/board`, so the board is the same for everyone with the link and survives
reloads and republishes. `localStorage` is the fallback when the shared store is
unavailable — the pill in the header says which is in use.

**This file embeds real distributor revenue figures** as its seed data. The published
artifact is private to its owner unless shared from the page's share menu.

### Updating it

Load next month's `2026_plan.xlsx` on the Targets tab — the `revenues` sheet layout is
recognised on its own (a `Country` column, a `Q3 target` column and a bare-year booked
column) and imports without a mapping step. Any other layout falls back to picking the
columns by hand. "Reload the sheet as supplied" restores the embedded September figures.

To change the app itself: edit `daily-desk/index.html`, then republish to the **same
URL** — from a Claude Code session, pass that artifact URL as `url` to the Artifact
tool. Publishing without the URL creates a separate artifact.

The page is authored as artifact body content: no `<!DOCTYPE>`, `<html>`, `<head>` or
`<body>` tags — those are added at publish time.
