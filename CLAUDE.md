# FitCoach Project Bible

## What This Is
A single-file HTML fitness coaching web app for Matt (Vancouver, BC). Built with vanilla HTML/CSS/JS, Firebase backend, and Google Gemini AI.

## Live URLs
- Primary: https://fitcoach-2a682.web.app (Firebase Hosting)
- Backup: https://mattsname.github.io/fitcoach/ (GitHub Pages)

## Deployment
Two steps required for every update:
1. git add . && git commit -m "description" && git push (updates GitHub Pages)
2. firebase deploy --only hosting (updates Firebase Hosting - primary URL)

## User Profile
- Name: Matt, Vancouver BC
- Age: 43, Height: 70.08in
- Goal weight: 168lbs, Calorie target: 2200, Protein target: 180g
- Gym: Mon/Wed/Fri (Push/Pull alternating), Swim: Tue or Thu
- Uses lbs, tracks weight daily in Google Sheets (Col A: YYYY/MM/DD, Col B: weight lbs)
- Primary device: Android (Pixel 9, Chrome), Secondary: MacBook

## Tech Stack
- Single file: index.html (2000+ lines)
- Firebase: fitcoach-2a682 (us-west1)
- Gemini API: gemini-2.5-flash model
- Auth: Google Sign-In (signInWithPopup)
- Hosting: Firebase Hosting + GitHub Pages

## Firebase Config
- Project ID: fitcoach-2a682
- Auth domain: fitcoach-2a682.firebaseapp.com
- Firestore paths: users/{uid}/profile/main, users/{uid}/weights/{date}, users/{uid}/food/{date}, users/{uid}/workouts/{date}

## Architecture
- State object holds: uid, apiKey, profile, weightLog, foodLog, chatHistory, progressPhotos, currentWorkout, customSchedule
- customSchedule: user overrides for push/pull/swim/rest per day, persisted to Firebase
- Food log: must read/write ONLY from Firebase Firestore (not localStorage)
- localStorage: only used for apiKey, profile, weightLog fallback when not signed in with Google

## UI Structure
4 tabs: Today, Log, Progress, Coach
- Today: Coach briefing + week strip + workout + nutrition summary
- Log: Weight logging + food photo/text analysis + food log
- Progress: Stats + weight chart (2 month window, scrollable) + progress photo (single slot) + body analysis
- Coach: Full chat with Gemini, quick chips

## Schedule Logic
Week A (even week): Mon=Push, Wed=Pull, Fri=Push
Week B (odd week): Mon=Pull, Wed=Push, Fri=Pull
Swim: Tue or Thu. Rest: Sun, Sat, Thu(A), Tue(B)
customSchedule overrides take priority over calculated schedule.

## Known Working Features
- Google Sign-In (signInWithPopup) - works on desktop
- Firebase sync for weights, food, workouts, profile
- Gemini coach chat with conversation history
- Schedule override via natural language ("make monday push day")
- Workout generation with coach conversation context
- Weight chart: 2-month scrollable window with year labels
- Nutrition rings (calories + protein) on Today tab
- Food analysis via photo or text description
- Google Sheets CSV import for weight history
- Favicon: orange FC on dark background

## Current Bugs / In Progress
- Google Sign-In failing on Android Chrome - investigating
- Android Sign-In error: "requested action is invalid" at fitcoach-2a682.firebaseapp.com/__/auth/handler
- Steps taken: Added web.app to authorized domains, API key restrictions, OAuth redirect URIs, JavaScript origins

## Design
- Dark theme: #0e0e0e background
- Orange accent: #ff5c1a
- Green: #3dffc0
- Font: Barlow Condensed
- Inspired by Wahoo cycling app aesthetic

## Important Notes
- Firebase API key is intentionally public (security via Firestore rules)
- Firestore rules: users can only read/write their own data
- API key restricted to mattsname.github.io/* and fitcoach-2a682.web.app/*
- Gemini API key stored in Firebase/localStorage, never hardcoded in HTML
- customSchedule saved to Firebase profile/main alongside profile data
