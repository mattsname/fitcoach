# FitCoach Project Bible

## What This Is
A single-file HTML fitness coaching web app for Matt (Vancouver, BC). Built with vanilla HTML/CSS/JS, Firebase backend, and Google Gemini AI.

## Live URLs
- PRIMARY: https://fitcoach-2a682.web.app (Firebase Hosting - use this on all devices)
- Backup: https://mattsname.github.io/fitcoach/ (GitHub Pages)

## Deployment
Two steps required for every update:
1. git -C ~/fitcoach add . && git -C ~/fitcoach commit -m "description" && git -C ~/fitcoach push
2. firebase deploy --only hosting (run from ~/fitcoach directory)
Or ask Claude Code to do both in one step.

## User Profile
- Name: Matt, Vancouver BC
- Age: 43, Height: 70.08in
- Goal weight: 168lbs, Calorie target: 2200, Protein target: 180g
- Gym: Mon/Wed/Fri (Push/Pull alternating), Swim: Tue or Thu
- Uses lbs, tracks weight daily in Google Sheets (Col A: YYYY/MM/DD, Col B: weight lbs)
- Primary device: Android (Pixel 9, Chrome), Secondary: MacBook

## Tech Stack
- Single file: index.html (2000+ lines)
- Firebase project: fitcoach-2a682 (us-west1)
- Gemini API: gemini-2.5-flash model
- Auth: Google Sign-In (signInWithPopup) - WORKING on all devices
- Hosting: Firebase Hosting (primary) + GitHub Pages (backup)

## Firebase Config
- Project ID: fitcoach-2a682
- Auth domain: fitcoach-2a682.firebaseapp.com
- API key restricted to: mattsname.github.io/*, fitcoach-2a682.web.app/*, fitcoach-2a682.firebaseapp.com/*
- Firestore paths: users/{uid}/profile/main, users/{uid}/weights/{date}, users/{uid}/food/{date}, users/{uid}/workouts/{date}

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

## Architecture
- State object: uid, apiKey, profile, weightLog, foodLog, chatHistory, progressPhotos, currentWorkout, customSchedule
- customSchedule: user overrides for push/pull/swim/rest per day, persisted to Firebase
- Food log: reads/writes ONLY from Firebase Firestore (not localStorage)
- localStorage: only used for apiKey, profile, weightLog as fallback when not signed in

## UI Structure
4 tabs: Today, Log, Progress, Coach
- Today: Coach briefing + week strip + workout + nutrition summary
- Log: Weight logging + food photo/text analysis + food log
- Progress: Stats + weight chart (2 month window, scrollable) + single progress photo slot + body analysis
- Coach: Full chat with Gemini, quick chips

## Schedule Logic
Week A (even week): Mon=Push, Wed=Pull, Fri=Push
Week B (odd week): Mon=Pull, Wed=Push, Fri=Pull
Swim: Tue or Thu. Rest: Sun, Sat, Thu(A), Tue(B)
customSchedule overrides take priority over calculated schedule.
Schedule changes detected via regex in user messages - no extra API call needed.

## Working Features
- Google Sign-In (signInWithPopup) - works on all devices and browsers
- Cross-device sync via Firebase Firestore - WORKING (food, weights, workouts, profile)
- Gemini coach chat with conversation history
- Schedule override via natural language ("make monday push day")
- Workout generation with coach conversation context
- Weight chart: 2-month scrollable window with year labels
- Nutrition rings (calories + protein) on Today tab
- Food analysis via photo or text description
- Google Sheets CSV import for weight history
- Favicon: orange FC on dark background
- Single progress photo slot

## Planned Features (not yet built)
1. Weekly nutrition view: show each day's calories vs target, deficit/surplus per day, weekly summary, correlate with weight loss
2. Workout history & progressive overload: save actual weights lifted per session, show "last session: X lbs" before each exercise, week-over-week strength comparison

## Design
- Dark theme: #0e0e0e background
- Orange accent: #ff5c1a
- Green: #3dffc0
- Font: Barlow Condensed
- Inspired by Wahoo cycling app aesthetic
