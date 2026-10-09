# One system: calendar + planner + Canvas

**Recommendation: Google Calendar as the hub, with Google Tasks for the planner
layer, and your Canvas feed subscribed into it.** Free, works on every device,
and it is the only setup here where Canvas due dates arrive without you doing
anything.

Total setup time: about 15 minutes. Daily upkeep: about 1 minute.

---

## Why this, and what else was considered

The deciding fact is technical. Canvas publishes your due dates as an **iCalendar
(.ics) feed** at a private URL. Something has to fetch that URL on a schedule.
Google and Apple do it on **their servers**, which is why it works unattended.

| | Canvas sync | Daily use | Free |
|---|---|---|---|
| **Google Calendar + Tasks** | Subscribes to the feed directly. Refresh is automatic but **slow and not adjustable** | Easiest. One grid, colour toggles, real search, tasks show in the calendar | Yes |
| **Apple Calendar + Reminders** | Also subscribes directly, and macOS lets you **set the refresh interval** (as often as every 5 min) | Very good if you are all-Apple. Calendar and Reminders are two apps | Yes |
| **Notion** | **No native feed subscription.** Needs a paid automation or Google in the middle | Most customisable, slowest to use daily, weakest mobile | Free plan is fine for one person |

**Apple Calendar is the better pick if you are all-Apple and fresh Canvas data
matters most**, purely because you can control the refresh interval. Everything
else below is identical; only the subscribe step differs (noted inline).

**Notion is the one to skip** for this goal. It cannot subscribe to the Canvas
feed on its own, so you would still need Google Calendar underneath — which adds
a layer instead of consolidating.

---

## Part 1 — Get your Canvas feed

1. Sign in at **https://pinecrest.instructure.com/**
2. Click **Calendar** in the far-left global navigation.
3. Scroll to the **bottom of the right-hand sidebar**, under the mini-month and
   the list of your courses. Click **Calendar Feed**.
4. A panel shows a URL like
   `https://pinecrest.instructure.com/feeds/calendars/user_XXXXXXXX.ics`
   Copy it.

> **Treat this URL like a password.** Anyone who has it can read all your
> assignment titles and due dates, with no login. Do not post it, and do not
> commit it to this repository — this repo is public.

**If there is no Calendar Feed button,** your school has disabled it. Skip to
*Fallbacks* below.

### Subscribe in Google Calendar

1. Open **calendar.google.com** on a computer (this cannot be done in the mobile app).
2. Left sidebar → **Other calendars** → **+** → **From URL**.
3. Paste the URL. If it begins `webcal://`, change that to `https://`.
4. Click **Add calendar**. It appears under *Other calendars*.
5. Hover it → **⋮** → **Settings**: rename it to `Canvas` and set the colour to
   **Tomato** (red).

### Subscribe in Apple Calendar instead

macOS: **File → New Calendar Subscription**, paste, **Subscribe**. Set
*Auto-refresh* to **Every hour** (or 5 minutes). Choose *Location: iCloud* so it
also reaches your iPhone — Apple's servers then handle the fetching.
iPhone alone: **Settings → Apps → Calendar → Accounts → Add Account → Other →
Add Subscribed Calendar**.

---

## Limitations — read this part

These are real, and none of them are your fault:

- **The refresh is delayed.** Google re-fetches subscribed feeds on its own
  schedule — often several hours, sometimes a day or more. You cannot force it.
  A due date your teacher adds this morning may not appear until tomorrow.
  *(This is the single reason to prefer Apple Calendar.)*
- **No alerts on subscribed calendars.** Google will not let you attach
  notifications to a feed you subscribed to. Deadline reminders have to come from
  Canvas itself or from your own tasks — both covered in Part 3.
- **It is read-only.** You cannot tick an assignment off or edit it from the
  calendar, and nothing you do there goes back to Canvas.
- **Only dated, published work appears.** No due date in Canvas means nothing on
  your calendar. Unpublished assignments, concluded courses, announcements, and
  To-Dos without dates are all absent.
- **The biggest gap is human.** If a teacher announces a test out loud or in a
  document without setting a Canvas due date, no tool can sync it. Part 2 gives
  that its own colour so you always know what is hand-entered.
- **Removing and re-adding** the calendar forces a fresh pull if it looks stale.

### Fallbacks if the feed is unavailable

1. **Canvas notifications** (works regardless): Canvas → **Account** →
   **Notifications** → set *Assignment Due Date* and *Due Date Changed* to
   **Notify immediately**. Install **Canvas Student** for push notifications.
2. **Per-course feeds:** each course's calendar can sometimes be subscribed
   separately even when the combined feed is off.
3. **Manual + weekly review:** Sunday, open Canvas → Dashboard → **To Do** list
   and the Canvas calendar's month view, and enter the week by hand. Ten minutes.
4. **Import into Semester Ledger** (Part 5) if you would rather keep this planner.

---

## Part 2 — Colour system: warm = school, cool = life

Create these as **separate calendars**, not just coloured events, so each one can
be toggled off. In Google Calendar: left sidebar → **Other calendars**... → for
your own, use **+ → Create new calendar**.

| Calendar | Colour | What goes in it |
|---|---|---|
| `Canvas` | **Tomato** (red) | Everything that arrived automatically. Never edit |
| `School – added by me` | **Tangerine** (orange) | Tests, projects and deadlines a teacher said out loud. **This is the one that saves you** |
| `Personal` | **Basil** (green) | Family, appointments, social |
| `Activities` | **Blueberry** (blue) | Sport, clubs, work shifts, lessons |

Four is the limit worth maintaining. The rule is one sentence: **warm colours are
school, cool colours are your own life.** You can tell at a glance without
reading a single word.

*If you have more than about 8 classes* and want per-class colour, do not make 8
calendars — keep the 4 above and set individual event colours inside `School –
added by me`. Per-calendar toggles stay useful; per-class ones do not.

---

## Part 3 — The planner layer

### A to-do list that lives in the calendar

**Google Tasks** is already in Google Calendar — no new app. Click the **Tasks**
icon in the right side panel (or add the Tasks layer from the sidebar).

Make exactly three lists:
- **Assignments** — things to hand in
- **Study** — revision sessions and reading
- **Life** — everything else

Give every task a **date**. Dated tasks appear **in the calendar grid**, so one
view shows events and work together, and you tick them off there.

### Reminders before deadlines

Because subscribed feeds cannot carry alerts, use two channels:

1. **Canvas does deadline reminders** (Part 1 fallback #1). This is the
   authoritative source and it is free. Set it once.
2. **Your own tasks do study reminders.** A task with a *time* notifies you on
   your phone. For a big test, make a task two days before called
   "Start studying — Euro Unit 3".

For anything genuinely critical, add a real event in `School – added by me` and
give it two notifications (1 day before, 2 hours before) — events you create
*can* have alerts.

### "What's due this week"

Set a 7-day view and make it your default:

1. Settings (gear → **Settings**) → **View options** → **Set custom view** →
   **7 days**.
2. Settings → **General** → **Start of week** and **Default view → Custom view**.
3. Now the keyboard shortcut **X** always gives you exactly the next 7 days.

To see *only* school work: untick `Personal` and `Activities` in the sidebar. Two
clicks, and the week is nothing but deadlines.

---

## Part 4 — Finding anything in seconds

Turn shortcuts on first: Settings → **General** → **Keyboard shortcuts** → on.

| Key | Does |
|---|---|
| `/` | **Search** every calendar by title. This is the fastest way to find anything |
| `A` | **Schedule view** — one scannable list of everything coming up. The best answer to "at a glance without digging" |
| `X` | Your 7-day week |
| `D` `W` `M` | Day / Week / Month |
| `T` | Jump to today |
| `J` / `K` | Next / previous period |
| `C` | Create an event |

Three habits that do the most work:

1. **Default to Schedule view (`A`) on mobile.** Open the app, see a list, done.
   Add the Google Calendar **widget** to your phone's home screen in Schedule
   mode and you will rarely open the app at all.
2. **Use sidebar checkboxes as filters.** Unticking is faster than searching.
3. **Sunday review, 10 minutes.** Press `X`. Read the week. Anything a teacher
   mentioned that is not there, add to `School – added by me`. This single habit
   is what makes the system trustworthy, because it closes the gap the Canvas
   feed cannot.

---

## Part 5 — Keeping Semester Ledger alongside (optional)

[Semester Ledger](../) is better than Google Calendar at one thing: seeing
**progress per class** — the ring, the per-class bars, priorities and notes. It
cannot pull Canvas automatically, for a concrete reason: Canvas serves its feed
without CORS headers, so a web page is not permitted to fetch it, and the Claude
artifact build blocks outbound requests entirely. Only a server can subscribe.

So it supports a **manual import** instead:

1. In Canvas, open the **Calendar Feed** URL in your browser. It downloads an
   `.ics` file.
2. In Semester Ledger: **gear → Data → Import from Canvas (.ics)** and pick the file.
3. Canvas **assignments** become tasks with their due dates; Canvas **events**
   become calendar events.

Notes on the import:
- **Re-importing updates, it never duplicates** — items are matched on their
  Canvas id. Anything you have already ticked off **stays ticked**.
- Assignments link to a class when the Canvas course name matches one of your
  class names. If it does not match, the item still imports with no class and the
  summary tells you which course names were unmatched — rename a class to match
  and import again.
- Times are converted from UTC to your local timezone, so an 11:59 PM deadline
  lands on the right day.
- Nothing is sent anywhere, and the feed URL is never stored.

**Honest recommendation:** if you want one system, put everything in Google
Calendar and treat this planner as optional. Two systems means two places to
check, which is the problem you are trying to solve. Use Semester Ledger if the
per-class progress view is worth a weekly manual import to you.

---

## Assumptions

Stated so you can correct them:

- **Mac + iPhone.** If you are on Windows or Android, Google Calendar is an even
  clearer pick and the Apple option disappears.
- **About 6–7 classes.** Under ~8, the 4-calendar colour scheme works as written;
  above that, see the note in Part 2.
- **Free tools only**, and one place for everything rather than a best-in-class app
  per job.
- **You want this to survive a busy week**, so the system is deliberately small:
  4 calendars, 3 task lists, 1 weekly review.

## 15-minute checklist

- [ ] Copy the Canvas Calendar Feed URL (Part 1)
- [ ] Subscribe to it in Google Calendar, rename to `Canvas`, colour Tomato
- [ ] Create `School – added by me`, `Personal`, `Activities` with their colours
- [ ] Turn on Canvas notifications for due dates, install Canvas Student
- [ ] Create the `Assignments`, `Study`, `Life` task lists
- [ ] Set custom view to 7 days and make it the default; enable keyboard shortcuts
- [ ] Put the Schedule-view widget on your phone's home screen
- [ ] Put a recurring 10-minute "Plan the week" event on Sunday
