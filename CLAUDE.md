# FitCoach — Claude Code Notes

## Source of Truth

**~/digital-twin/FITCOACH-BIBLE.md is the source of truth** for current UI
structure, state object shape, working/planned features, architecture, and
the Wear OS watch app. It is updated every session.

This file covers only what rarely changes: auth, deployment, project config,
design tokens, and the handful of facts a session needs before opening
anything else. When the two disagree, the bible wins — and this file should
be corrected, not worked around.

(History: this file previously described a 4-tab app and an A/B alternating
schedule, both long superseded. A session trusting it renamed the Analysis
tab back to "Coach". Hence the delegation above.)

## What This Is
A single-file HTML fitness coaching web app for Matt (Vancouver, BC). Built
with vanilla HTML/CSS/JS, Firebase backend, and Google Gemini AI.

## Live URLs
- PRIMARY: https://fitcoach-2a682.web.app (Firebase Hosting - use this on all devices)
- Backup: https://mattsname.github.io/fitcoach/ (GitHub Pages)

## Deployment
Two steps required for every update:
1. git -C ~/fitcoach add . && git -C ~/fitcoach commit -m "description" && git -C ~/fitcoach push
2. firebase deploy --only hosting (run from ~/fitcoach directory)
Or ask Claude Code to do both in one step.

Run the manual test checklist before deploying — never deploy unverified.

Syntax check before deploy: index.html's script is `type="module"`, so
`new Function(src)` gives a FALSE NEGATIVE ("Cannot use import statement
outside a module"). Extract the script block to a `.mjs` file and run
`node --check` on that instead.

`firebase deploy` reads firebase.json from the CURRENT directory — always
run it from ~/fitcoach, or it will deploy a different project's files.

## Firebase Config
- Project ID: fitcoach-2a682 (us-west1)
- Auth domain: fitcoach-2a682.firebaseapp.com
- Gemini API: gemini-2.5-flash model
- API key restricted to: mattsname.github.io/*, fitcoach-2a682.web.app/*, fitcoach-2a682.firebaseapp.com/*

### Firestore Paths
- users/{uid}/profile/main
- users/{uid}/weights/{date}
- users/{uid}/food/{date}
- users/{uid}/workouts/{date} — per-day workout doc; primary read source for the watch app
- users/{uid}/exerciseHistory/{exerciseName} — sessions array + personalBest
- users/{uid}/swims/{YYYY-MM-DD} — FORM CSV swim sessions

(Doc shapes and field details: see the bible.)

## Auth Setup (IMPORTANT - hard won)
- signInWithPopup with GoogleAuthProvider
- authDomain must be fitcoach-2a682.firebaseapp.com (not web.app)
- Google Cloud OAuth client must have these authorized JS origins:
  http://localhost, http://localhost:5000, https://fitcoach-2a682.firebaseapp.com, https://fitcoach-2a682.web.app, https://mattsname.github.io
- Authorized redirect URIs:
  https://fitcoach-2a682.firebaseapp.com/__/auth/handler
  https://fitcoach-2a682.web.app/__/auth/handler
  https://mattsname.github.io/fitcoach/
- API key website restrictions MUST include fitcoach-2a682.firebaseapp.com/* or auth popup breaks
- Gemini API key stored in Firebase profile/main and localStorage, never hardcoded

Local testing must use port 5000 (`firebase serve --only hosting --port 5000`)
— other ports are not authorized origins and the sign-in popup will fail.

## UI Structure
5 tabs, left to right: **Today, Log, Swim, Progress, Analysis**.

CORRECTION: this file previously said "4 tabs: Today, Log, Progress, Coach".
There is no "Coach" tab — it was renamed Analysis, and Swim was added in
July 2026. Do NOT rename tabs unless explicitly asked.

Per-tab contents, surfaces, and charts: see the bible.

## Schedule Logic
Fixed weekly split — no epoch math, no rolling cycle:
```
WEEKLY_SCHEDULE = { 0:'rest', 1:'push', 2:'swim', 3:'pull', 4:'swim', 5:'legs', 6:'rest' }
```
DOW: 0=Sun. So Mon=Push, Tue=Swim, Wed=Pull, Thu=Swim, Fri=Legs, Sat/Sun=Rest.

CORRECTION: this file previously described an A/B alternating scheme
("Week A: Mon=Push, Wed=Pull, Fri=Push; Week B: inverted") with no Legs day.
That model was replaced in July 2026. Legs is a real workout type with its
own exercise set.

`customSchedule` overrides take priority over WEEKLY_SCHEDULE. Schedule
changes are detected via regex in user messages — no extra API call.

## User Profile
- Name: Matt, Vancouver BC
- Age: 43, Height: 70.08in
- Goal weight: 168lbs, Calorie target: 2200, Protein target: 180g
- Gym: Mon/Wed/Fri (Push/Pull/Legs fixed split), Swim: Tue/Thu
- Uses lbs, tracks weight daily in Google Sheets (Col A: YYYY/MM/DD, Col B: weight lbs)
- Primary device: Android (Pixel 9, Chrome), Secondary: MacBook

## Design
- Dark theme: #0e0e0e background
- Orange accent: #ff5c1a
- Green: #3dffc0
- Font: Barlow Condensed
- Inspired by Wahoo cycling app aesthetic

## Working Rules
- Only change what was requested. No "improvements" to adjacent UI, labels, or names.
- Search for existing variable names before declaring new module-level `let`/`const`
  — the single ~6000-line script has a history of collisions (a duplicate
  `let swimChartMetric` caused a boot crash).
- The code is source of truth, not the docs. Validate lists (e.g. exercises)
  with a name-level diff against `git show HEAD:index.html`, not a count.

## Delegated to ~/digital-twin/FITCOACH-BIBLE.md
Not duplicated here, to avoid a second copy drifting:
- Full per-tab UI descriptions, Progress calendar, exercise history lookup
- State object key list (18 keys) and `window.__fc` debug hook
- Working features (Swim tab, FORM CSV import, isometric logging, PBs,
  Analysis charts, retroactive editing, meal timing, weekly nutrition view)
- Planned features and known bugs/backlog
- Wear OS watch companion app (~/fitcoach-watch) — separate native app
- Exercise library, session rules, workout generation pipeline
- FORM swim parser, pool-size handling, splits
