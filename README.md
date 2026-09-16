# Semester Ledger

A single-file school planner in dark glass: cream on near-black. Tasks are organized by
class, filed automatically by how soon they are due, and checked off from the
dashboard. A progress ring fills as you complete them.

[![Deploy to GitHub Pages](https://github.com/Zynx0-ops/semester-ledger/actions/workflows/pages.yml/badge.svg)](https://github.com/Zynx0-ops/semester-ledger/actions/workflows/pages.yml)

**Try it:** <https://zynx0-ops.github.io/semester-ledger/>

## Features

- **Classes as categories.** Each class carries a colour and an icon, shown on
  every task that belongs to it, with its own progress bar and `done/total`.
- **Reorderable classes.** Tap *Edit* on the Classes card and drag the handles
  into your real period order — by pointer or touch, or with the arrow keys on a
  focused handle. The order drives both the sidebar and the class-grouped list.
- **Due dates and times.** Tasks file themselves into *Overdue*, *Today*,
  *Tomorrow*, *This Week*, *Later* and *No Date* — urgency order, not entry
  order. Overdue reads red, today's reads in the accent colour.
- **Check it off.** Tap the box; the task strikes through, moves to *Completed*,
  and every counter and the ring update at once.
- **Progress ring** that rescopes to whichever class you select.
- **Notes and priority** per task, edited in a detail sheet.
- **Undo** on every destructive action — deleting a task, deleting a class (and
  its tasks), and completing a task while *Show Completed* is off.

### Customization

Everything below lives in the Settings sheet and syncs with your tasks.

| Setting | Options |
|---|---|
| Theme | Auto · Light · Dark |
| Accent colour | Ivory (default) · 9 colours |
| Checkbox | Square · Circle |
| Row height | Roomy · Compact |
| Group by | Due date · Class |
| Sort within groups | Due · Priority · Name · Newest |
| Show completed | On · Off |
| Class colour & icon | 9 colours, 15 icons, per class |
| Class order | Drag to match your timetable |

## Design

Dark glass, monochrome by intent. A warm near-black ground lit by a soft glow
from the upper left; translucent cards with 1px hairline borders and 22px radii;
cream type instead of pure white; oversized tight-tracked numerals; and small
uppercase, letter-spaced labels naming every card, field and section. Inter
supplies the neutral grotesque, with the system stack behind it.

Colour is spent in one place. The default **Ivory** accent is cream in dark mode
and near-black ink in light mode, so the chrome carries no hue and class colours
are the only chroma on the page. Each accent stores the colour drawn on top of it
per theme — without that, a checkmark, switch knob or swatch tick would be white
on cream and vanish.

Light mode is a warm-paper twin rather than an inversion. The header stays clear
at the top of the page and becomes a blurred glass bar, with a smaller title,
once content scrolls beneath it. Sheets become bottom sheets on a phone.

Grouping by class hides the per-row class chip, since the section header already
names it — one of several places where the chrome reflects the current view
rather than repeating it. The colour does not go with it: the section header
carries the class's coloured icon and each row's checkbox is tinted to match,
the way Reminders tints by list. Each grouping mode therefore has exactly one
colour carrier rather than two competing ones.

## Where it runs

| Where | Saving |
|---|---|
| **GitHub Pages** — [zynx0-ops.github.io/semester-ledger](https://zynx0-ops.github.io/semester-ledger/) | This browser only (`localStorage`) |
| **Claude Artifact** | Syncs across devices via `data/planner.json` |

The two are independent: tasks added in one do not appear in the other. On Pages,
tasks live only in the browser that created them, so clearing site data erases
them — use **Settings → Download a Backup**.

### On accounts

There is deliberately no login. Real accounts need a server to hold credentials
and per-user data, and GitHub Pages only serves static files, so the only honest
options are a backend service (Supabase, Firebase) or nothing. A password checked
in client-side JavaScript would be readable by anyone in devtools and would sync
nothing, so it is not implemented. The Artifact copy cannot host accounts either:
artifacts block outbound network calls, a `db`-declaring artifact cannot be shared
publicly, and the `user` capability needed to tell viewers apart is unavailable.

## Data model

```
state = { v, updatedAt, settings, classes[], tasks[] }
class  = { id, name, color, icon }
task   = { id, classId, title, note, due, time, done, doneAt, priority, createdAt }
```

`normalize()` accepts v1 documents (no `settings`, no `note`/`priority`/`icon`)
and v2 documents, and fills defaults, so older saved data keeps working. Data
saved before the glass redesign (below v3) opens once in the Ivory accent to match
it; the next save writes v3, after which any accent you pick is kept. Migration happens in
memory on load and is only written back on the next change — loading the app
never rewrites your data. A colour outside the current palette is preserved and
added to the picker rather than being reset.

Two layers persist it: `data/planner.json` next to the page via the Artifact
`artifact` capability (files form, which avoids reloading the page on save), and
`localStorage` written synchronously as a mirror and offline fallback. On load the
newer `updatedAt` wins; writes are debounced and coalesced. If cloud saving is
unavailable the app degrades to local-only and says so in Settings.

Dates are `YYYY-MM-DD` strings compared lexically, and only ever parsed into a
`Date` from explicit parts — never `new Date("...")`, which shifts by a day
across timezones.

### Appearance resolution

The viewer's theme has three states (explicit `data-theme` stamp, or nothing at
all for "system"), and the app adds its own preference on top. An inline script
resolves all of it into one `data-appearance` attribute before first paint, so a
dark viewer never sees a light flash, and a `MutationObserver` plus a
`matchMedia` listener keep *Auto* in step when the host or OS theme changes.

## Running it

`planner.html` is a body fragment, not a complete document — it is authored for
the Claude Artifact publisher, which supplies the `<!doctype>` / `<head>` /
`<body>` skeleton. The [Pages workflow](.github/workflows/pages.yml) wraps it on
every push to `main`. To run it locally, wrap it the same way:

```bash
printf '<!doctype html>\n<html><head><meta charset="utf-8">\n<meta name="viewport" content="width=device-width,initial-scale=1">\n</head><body>\n' > index.html
cat planner.html >> index.html
printf '\n</body></html>\n' >> index.html
python3 -m http.server 8000
```

Then open <http://localhost:8000>. Served this way `window.claude` is absent, so
`claude.use()` resolves `null` and the app runs in local-only mode by design.
