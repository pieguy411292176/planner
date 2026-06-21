# Deploy + Google Calendar setup

Two parts: **(A)** put the site online, **(B)** turn on live Google Calendar sync.

> ✅ **Part A is DONE.** The site is live at **https://pieguy411292176.github.io/planner/**
> (repo: https://github.com/pieguy411292176/planner). To update later, just
> `git add -A && git commit -m "..." && git push` — Pages redeploys automatically.
> Part A steps are kept below for reference. **Do Part B next** to turn on Google Calendar.

---

## Part A — Put it online (GitHub Pages, free) — ✅ already done

The local repo is already initialized and committed on the `main` branch. You just need a
GitHub repo to push to.

### 1. Create an empty repo on GitHub
- Go to https://github.com/new
- Repository name: `planner`
- Visibility: **Public** (required for free GitHub Pages)
- **Do NOT** check "Add a README" / .gitignore / license (the repo already has files)
- Click **Create repository**

### 2. Push the code
Copy the commands GitHub shows under "…or push an existing repository", or run these
(replace `YOURNAME` with your GitHub username) from the `planner` folder:

```bash
git remote add origin https://github.com/YOURNAME/planner.git
git push -u origin main
```

On the first push, a browser window pops up to sign in to GitHub (Git Credential Manager
ships with Git for Windows). Approve it once and you're done.

### 3. Turn on Pages
- In the repo: **Settings → Pages**
- "Build and deployment" → Source: **Deploy from a branch**
- Branch: **main**, folder: **/ (root)** → **Save**
- Wait ~1 minute. Your site is live at:

      https://YOURNAME.github.io/planner/

Open that on your phone → browser menu → **Add to Home Screen** to use it like an app.

> Updating later: edit files, then `git add -A && git commit -m "update" && git push`.
> Pages redeploys automatically.

---

## Part B — Live Google Calendar sync

This makes the **deployed site** read your Google Calendar in the browser. It needs a public
OAuth Client ID (not a secret). ~5–10 minutes, one time.

### 1. Create a Google Cloud project
- Go to https://console.cloud.google.com/
- Top bar → project dropdown → **New Project** → name it `planner` → Create → select it.

### 2. Enable the Calendar API
- Search bar → "Google Calendar API" → **Enable**.

### 3. Configure the consent screen
- **APIs & Services → OAuth consent screen**
- User type: **External** → Create
- App name: `Planner`, your email for support + developer contact → Save & continue
- Scopes: skip (we request the read-only scope from code) → Save & continue
- **Test users → Add users → your Gmail address** (michaelzhou4112@gmail.com) → Save
  - (While the app is in "Testing" mode, only listed test users can connect — that's fine.)

### 4. Create the OAuth Client ID
- **APIs & Services → Credentials → Create Credentials → OAuth client ID**
- Application type: **Web application**
- Name: `Planner web`
- **Authorized JavaScript origins → Add URI**, add BOTH:
  - `https://pieguy411292176.github.io`  ← your Pages origin (no path)
  - `http://localhost:8770`              ← optional, for testing locally
- Create. Copy the **Client ID** (looks like `1234-abc.apps.googleusercontent.com`).

### 5. Connect in the app
- Open your deployed site → click **📅 Google** (top right)
- Paste the Client ID → **Connect** → sign in with your Gmail in the popup
- Pick which calendars to show. Your Google events now appear (green chips, read-only).

> The button shows **Google ✓** when connected. Tokens last ~1 hour; just hit Connect again
> to refresh. This is read-only for now — edits made in the planner are not pushed back to
> Google (that's a possible later upgrade).

### Troubleshooting
- **"redirect_uri_mismatch" / origin error** → the Authorized JavaScript origin must exactly
  match your site origin (scheme + host, no trailing slash, no path).
- **"access_denied"** → add your Gmail under OAuth consent screen → Test users.
- **Popup blocked** → allow popups for the site and click Connect again.

---

## Part C — Cross-device sync (Firebase)

This makes your **tasks, events, and labels** sync between your laptop and phone in near
real-time. You sign in with Google on each device; everything lives in your own private
Firebase space. Free. ~10 minutes, one time. (Separate from Part B — Calendar sync — though
both use your Google account.)

### 1. Create a Firebase project
- Go to **https://console.firebase.google.com/** → **Add project**.
- Name it `Planner` (you can reuse the existing Google Cloud `Planner` project if it's offered).
- Google Analytics: not needed — turn it off → **Create project**.

### 2. Create the database
- Left menu → **Build → Firestore Database** → **Create database**.
- Start in **Production mode** → pick the closest location → **Enable**.

### 3. Lock the database to each user (security rules)
- In Firestore → **Rules** tab → replace everything with this, then **Publish**:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /planners/{uid} {
      allow read, write: if request.auth != null && request.auth.uid == uid;
    }
  }
}
```
This means only *you*, signed in, can read/write your own data.

### 4. Turn on Google sign-in
- Left menu → **Build → Authentication** → **Get started**.
- **Sign-in method** tab → **Google** → enable → set your email as support email → **Save**.
- **Settings** tab → **Authorized domains** → **Add domain** → `pieguy411292176.github.io`
  (localhost is already allowed for testing).

### 5. Get your config snippet
- Click the **gear ⚙ → Project settings** → scroll to **Your apps**.
- Click the **web icon `</>`** → register app nickname `Planner` (skip Hosting) → **Register app**.
- Copy the **`firebaseConfig`** object it shows (the block with `apiKey`, `projectId`, etc.).

### 6. Connect in the app
- Open your site → click **☁ Sync** → paste the whole config into the box → **Save & Sign in**.
- A Google popup appears → choose your account → **Allow**.
- The button changes to **Synced ✓**. Do the same on your phone (same Google account) and your
  data appears on both, updating live.

> Notes
> - First device to sign in uploads your existing local data; after that all devices share it.
> - It's "whole-document" sync — if you edit the *same* item on two devices while one is
>   offline, the last save wins. For normal use (laptop, then phone) this is seamless.
> - Not signed in → the app still works fully, just locally on that device.

### Troubleshooting
- **`auth/unauthorized-domain`** → add `pieguy411292176.github.io` under Authentication →
  Settings → Authorized domains (step 4).
- **`Missing or insufficient permissions`** → re-check the Firestore Rules in step 3.
- **Config rejected** → make sure you pasted the full object including `apiKey` and `projectId`.
