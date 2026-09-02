# Semester Ledger

A single-file school planner. Tasks are organized by class, filed automatically by
how soon they are due, and checked off with a square checkbox on the dashboard.
A progress wheel in the sidebar fills as you complete them.

![status](https://img.shields.io/badge/build-single%20file-0E5A69)

## Features

- **Classes as categories.** Each class gets its own colour, shown as a rail down
  the left edge of every task that belongs to it.
- **Due dates and times.** Tasks file themselves into *Overdue*, *Today*,
  *Tomorrow*, *This week*, *Upcoming*, and *No date set* — urgency ordering
  rather than entry order. Overdue dates render in red, today's in the accent.
- **Check it off.** Click the square next to a task; it strikes through, moves to
  a collapsible *Completed* section, and every counter updates at once.
- **Progress wheel.** Fills as tasks are completed. Select a class in the sidebar
  and the wheel rescopes to that class; each class also carries its own progress
  bar and `done/total` count.
- **Inline editing**, delete with an **Undo** toast, and a two-step confirm for
  deleting a class that names how many tasks go with it.
- **Light and dark**, following the viewer's theme.

## How data is stored

The planner has no backend. It persists through two layers:

1. **`data/planner.json`**, written next to the page via the Claude Artifact
   `artifact` capability (files form). This is what makes tasks follow you
   between devices and browsers.
2. **`localStorage`**, written synchronously on every change as an instant local
   mirror and offline fallback.

On load, both are read and the newer `updatedAt` wins. Writes are debounced ~1.4s
and coalesced into one save. If cloud saving is unavailable — a read-only view, a
missing capability, a rate limit — the app degrades to local-only storage and says
so in the status chip rather than failing silently or losing work.

## Running it

`planner.html` is a body fragment, not a complete document — it is authored for
the Claude Artifact publisher, which supplies the `<!doctype>` / `<head>` /
`<body>` skeleton. To run it standalone, wrap it:

```bash
printf '<!doctype html>\n<html><head><meta charset="utf-8">\n<meta name="viewport" content="width=device-width,initial-scale=1">\n</head><body>\n' > index.html
cat planner.html >> index.html
printf '\n</body></html>\n' >> index.html
python3 -m http.server 8000
```

Then open <http://localhost:8000>. Served this way `window.claude` is absent, so
`claude.use()` resolves `null` and the app runs in local-only mode by design.

## Structure

Everything lives in `planner.html`:

- **CSS tokens** — the complete light palette on bare `:root`; dark redefines the
  same 18 tokens twice, once under `prefers-color-scheme` (guarded so an explicit
  light choice wins) and once under `[data-theme="dark"]`.
- **State** — `{ classes[], tasks[], updatedAt }`, mutated only through `commit()`,
  which stamps the timestamp, mirrors to localStorage, re-renders, and schedules a save.
- **Rendering** — full re-render from state on change. The progress wheel is the
  one exception: it lives in static markup and is updated by attribute so its
  `stroke-dashoffset` transition animates.

Dates are handled as `YYYY-MM-DD` strings compared lexically and only ever parsed
into a `Date` from explicit parts, never via `new Date("...")`, which would shift
by a day across timezones.
