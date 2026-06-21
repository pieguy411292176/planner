# Deploy + Google Calendar setup

Two parts: **(A)** put the site online, **(B)** turn on live Google Calendar sync.
Do A first — B needs the live URL from A.

---

## Part A — Put it online (GitHub Pages, free)

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
  - `https://YOURNAME.github.io`  ← your Pages origin (no path)
  - `http://localhost:8770`        ← optional, for testing locally
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
