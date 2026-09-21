# Strength Log

An offline exercise tracker for logging **sets, reps and weight in kg**, plus a
daily **mobility habit** tracker for the recovery side of the programme.

No build step, no server, no account, no network calls. `index.html` is the whole
app — open it and it works. Everything you log stays in that browser's local
storage on that device.

## Getting it on your phone

**Option A — GitHub Pages (recommended, gives you a home-screen app)**

1. In this repo: **Settings → Pages → Source: Deploy from a branch**, pick the
   branch and `/ (root)`.
2. Open the published URL on your phone.
3. iOS Safari: **Share → Add to Home Screen**. Android Chrome: **⋮ → Add to Home
   screen**. It then opens full-screen and works with no signal — the service
   worker (`sw.js`) caches it on first visit, which matters in a basement gym.

**Option B — just the file**

Download `index.html` and open it. Everything works except home-screen install
and service-worker caching (browsers only allow those over http/https).

> Each browser/device holds its own separate data. There is no sync. To move
> data between devices, use **Settings → Export JSON**, then **Import JSON** on
> the other one.

## Using it

### Train
- Pick the date (defaults to today) and a workout template, then **Load template
  exercises** to drop that day's lifts in. Or go freeform with **+ Add exercise**.
- Each set row is `kg` × `reps` and a tick. Tick it when the set is done.
- **New sets pre-fill from your last set** — same exercise, this session if
  there is one, otherwise your most recent session with it. Between sets you only
  change what actually changed.
- The line above each exercise shows what you did last time, so you know what to
  beat.
- Ticking a set **starts the rest timer** using that exercise's default rest
  (set per exercise in Settings). `+30s` extends it, `Skip` dismisses it. It
  beeps and vibrates when time is up.
- A green **PR** badge marks a set that is your best estimated 1RM for that lift.
- Session notes at the bottom — useful for the recovery log ("right hip tight,
  went lighter").

### History
Every session, newest first. Tap one to expand the full set-by-set record, jump
back into it on the Train tab, or delete it. Tiles up top cover the last 30 days.

### Progress
Per exercise:
- **Heaviest set**, **best estimated 1RM**, **best session volume**, and the
  estimated-1RM trend across the selected range (90d / 6m / 1y / all).
- A chart of **top set** and **estimated 1RM** over time. Both are kg, so they
  share one axis — no misleading second scale. Tap or hover for the exact
  numbers; "View as table" gives the same data as text.
- **Volume per session** for that lift (weight × reps over completed sets).
- A **personal records** table across your whole library.

Estimated 1RM uses the Epley formula, `weight × (1 + reps / 30)`. A single at a
given load returns that load. It is an estimate for tracking trend, not a number
to go and attempt.

### Mobility
- Tick off each daily drill. **Mark all done** does the lot.
- **Current streak** (days with at least one drill), **full-set streak** (days
  where everything got ticked), **longest streak**, and a 30-day completion
  percentage.
- A month heatmap — darker means more drills that day. Tap any day to log
  retroactively; the arrows move between months. Missing today does not break a
  streak until the day is actually over.

### Settings
Theme, default rest, bodyweight (used to compute volume on bodyweight exercises),
rest-timer sound, plus full editing of the exercise library and workout templates.

## Making it your routine

The app ships with a general strength-and-recovery set so it is usable
immediately. Two ways to make it yours:

**In the app** — Settings → exercise library and templates. Add, rename, retype,
change rest times, build your own templates. Nothing is fixed.

**In the code** — edit the `SEED` block near the top of the `<script>` in
`index.html`. It only applies to a fresh install (a browser with no saved data),
so change it before you start logging, or export → edit → import.

```js
const SEED = {
  exercises: [
    { name: "Back Squat", group: "Lower", type: "weight", rest: 180 },
    ...
  ],
  templates: [
    { name: "Lower A", exercises: ["Back Squat", "Romanian Deadlift", ...] },
  ],
  habits: [ "90/90 hip rotations", ... ]
};
```

`type` decides what a set records:

| type | logs | volume counted as |
|---|---|---|
| `weight` | kg × reps | kg × reps |
| `bodyweight` | reps, plus any added kg | (bodyweight + added kg) × reps |
| `time` | seconds, plus optional kg (loaded carries) | not counted |

## Backing up

Local storage is not a backup. Clearing site data, an aggressive "clean up
storage" setting, or a lost phone all take your history with them.

- **Export JSON** — the complete state, and the only format **Import JSON**
  accepts. This is your backup.
- **Export CSV** — every set as a row (date, workout, exercise, set number, kg,
  reps, seconds, completed, estimated 1RM, volume, notes) with the habit log
  appended underneath. For spreadsheets or sharing with a coach or physio.

## Files

| File | What it is |
|---|---|
| `index.html` | The entire app — markup, styles, logic, charts. Works standalone. |
| `sw.js` | Service worker for offline caching. Only used over http/https. |
| `manifest.webmanifest` | Home-screen install metadata. |
| `icon.svg` | App icon. |

The charts are hand-rolled SVG rather than a charting library, so the app stays a
single dependency-free file that loads with no network.
