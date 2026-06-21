# Planner

A single-file planning app: calendar (Day / Week / Month / Year) + a sectioned to-do list
(Daily / Weekly / Monthly / Recruiting) that visually links to the calendar.

No build step, no dependencies. Just `index.html`.

## Run it locally
Double-click `index.html`, or open it in any browser. Data is saved automatically in that
browser's local storage.

## Features
- **Calendar views**: switch with the Day/Week/Month/Year buttons (or keys `1`–`4`).
  - Click any day/time slot to add an event.
  - Arrow keys ←/→ move through time; `t` jumps to today.
- **To-do sidebar** with four sections. Give any task a **due date** and it shows up on the
  calendar as a colored dot/chip (green=daily, amber=weekly, pink=monthly, blue=recruiting).
- **Recruiting** section: one card per company, each with a free-text "Next steps" box.

## Use it on your phone
Local storage lives per-device, so to reach it from your phone you need to host the file.
Easiest options:

1. **GitHub Pages** (recommended — same as your eth-trading-framework):
   - Create a repo, push this folder, enable Pages on the `main` branch.
   - Visit the URL on your phone, then **Add to Home Screen** to use it like an app.
2. **Netlify / Vercel drag-and-drop**: drop the folder on their dashboard for an instant URL.

> Note: data does **not** sync between devices yet — each browser keeps its own copy.
> Cross-device sync and Google Calendar import both need a small backend; see below.

## Coming later (stubbed for easy add)
- **Google Calendar sync**: drop in the Google Calendar API (OAuth) and map its events into
  the `state.events` array. The render layer already reads from that array.
- **Cross-device sync**: swap `localStorage` for a tiny backend (e.g. Firebase, Supabase) by
  replacing the `load()` / `save()` functions.
- **Desktop/phone app**: wrap with Tauri or package as a PWA (add a manifest + service worker).
