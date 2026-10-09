# Semester Ledger

A single-file school planner built on the NYC Subway sign system. Tasks are organized by
class, filed automatically by how soon they are due, and checked off from the
dashboard. A progress ring fills as you complete them.

[![Deploy to GitHub Pages](https://github.com/Zynx0-ops/semester-ledger/actions/workflows/pages.yml/badge.svg)](https://github.com/Zynx0-ops/semester-ledger/actions/workflows/pages.yml)

**Try it:** <https://zynx0-ops.github.io/semester-ledger/>

## Features

- **Classes as categories.** Each class carries a colour and an icon, shown on
  every task that belongs to it, with its own progress bar and `done/total`.
- **Calendar with events.** A month grid in the sidebar: navigate months, pick a
  day, and add, edit or delete events on it. Days carrying events show a dot per
  event in its class colour; days with an open task due are underlined; today is
  outlined and the selected day is filled. Deleting an event is undoable.
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
| Line colour | 9 MTA line colours (default N Q R W yellow) · Mono |
| Checkbox | Square · Circle |
| Row height | Roomy · Compact |
| Group by | Due date · Class |
| Sort within groups | Due · Priority · Name · Newest |
| Show completed | On · Off |
| Class bullet | 10 line colours, 15 icons, per class |
| Class order | Drag to match your timetable |

## Design

Modelled on the New York City Transit Authority signage system — the Vignelli and
Noorda standard — because that is the reference this redesign was given.

- **Black enamel signs.** The header is a station sign; section headers, card
  headers and sheet headers are black sign bars with white tracked capitals. They
  stay black in both themes, because the sign is the sign.
- **Subway tile.** Light mode hangs those signs on a running-bond white tile
  field, drawn as an inline SVG pattern. Dark mode is flat black enamel.
- **Route bullets.** Every class renders as a circular route bullet in its line
  colour, carrying its icon or its initial — on task rows, on class-grouped
  section headers, on calendar events, and across the top of the station sign.
- **Helvetica.** `Helvetica Neue` first, which is the real face on Apple devices
  and is what the system switched to in 1989. Archivo is loaded as the fallback so
  the grotesque holds on Windows and Android; Arial and Liberation Sans follow it.
- **Colour only in bullets.** In the real system type is never coloured — it is
  white on black or black on white, and colour is reserved for route bullets. So
  interactive text here takes full-contrast ink and the line colour is spent on
  bullets, the progress arc, the selected day and the primary button. Red is the
  1-2-3 red and marks overdue work.
- **Square corners, hairline rules, tracked capitals, tabular figures.** Buttons
  are rectangles, not pills. Segmented controls are divided boxes.

Grouping by class hides the per-row class chip, since the section header already
carries that class's bullet — each grouping mode has exactly one colour carrier
rather than two competing ones.

## Canvas and calendar setup

[docs/CALENDAR-SETUP.md](docs/CALENDAR-SETUP.md) covers consolidating school and
personal scheduling into one system: which calendar tool to use and why, how to
subscribe to a Canvas `.ics` feed (and what that feed will and will not deliver),
a colour scheme that separates school from personal at a glance, and how to add
planner-style to-dos and reminders on top.

It also documents this planner's **Canvas import** — Canvas serves its feed with
no CORS headers, so no web page may fetch it; the import reads a downloaded `.ics`
file instead, matches items on their Canvas id so re-importing updates rather than
duplicates, and never stores the feed URL.

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
state = { v, updatedAt, settings, classes[], tasks[], events[] }
class  = { id, name, color, icon }
task   = { id, classId, title, note, due, time, done, doneAt, priority, createdAt }
event  = { id, date, title, time, note, classId, createdAt }
```

Events share the planner's storage, so they persist exactly like tasks and need no
separate setup. A class and its events are deliberately independent: deleting a
class clears the class off its events rather than deleting them, and that is undoable.

`normalize()` accepts v1, v2 and v3 documents and fills defaults, so older saved
data keeps working — a document with no `events` array simply gains an empty one.
Data saved before the signage redesign (below v4) opens once in line yellow to match
it; the next save writes v4, after which any line colour you pick is kept. Migration happens in
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
