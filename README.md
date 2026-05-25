# Life Tracker

> Built and designed by **[Dipak Shaw](https://github.com/dipak-shaw)** — a single-file life-planning app born out of wanting one tool that ties 10-year vision down to today's tasks. Released under MIT so you can fork, host, and adapt it to your own goals.

A single-file life tracker that helps you plan and review goals & tasks across a 10-year horizon → year → semester → quarter → month → week → day. All data is stored locally, and optionally synced across devices via your own Google Drive.

> **Single HTML file. No build step. No backend. No npm.** Just open `index.html`.

---

## Features

- 10-year overview down to per-day tasks
- Goal hierarchy: Year → Semester → Quarter → Month → Week, per category (Personal, Health, Wealth, Travel, Branding, Content)
- Recurring tasks, carry-forward missed tasks, Google Calendar quick-add
- Calendar view with category dots, time chips, and week highlight
- Light / dark theme
- JSON import / export
- Optional **Google Drive sync** — your data lives in your own Drive `appDataFolder` (private to this app)

---

## Quick start (local only, no setup)

1. Download or clone the repo.
2. Open `index.html` in your browser.
3. Click **Skip — use local only** in the setup panel.

That's it — everything works offline against `localStorage`.

---

## Enable Google Drive cross-device sync

Drive sync needs a free OAuth 2.0 Client ID that you control. Setup is one-time and takes about 3 minutes.

### 1. Create a Google Cloud project

1. Open <https://console.cloud.google.com/>.
2. Top bar → project dropdown → **NEW PROJECT** → name it (e.g. `life-tracker`) → **Create**.

### 2. Enable the Drive API

1. <https://console.cloud.google.com/apis/library/drive.googleapis.com>
2. Click **Enable**.

### 3. Configure the OAuth consent screen

1. <https://console.cloud.google.com/apis/credentials/consent>
2. Choose **External** → Create.
3. Fill in:
   - App name: `Life Tracker`
   - User support email: your email
   - Developer contact: your email
4. **Scopes** step: skip (the app requests `drive.appdata` at runtime).
5. **Test users** → **+ ADD USERS** → add your own Gmail address (and anyone else who'll use your hosted copy). Save.

> Staying in **Testing** mode is fine for personal use. You can support up to 100 test users. If you ever want anyone with a Google account to sign in, click **PUBLISH APP** — users will see a one-time "unverified app" warning that they dismiss via *Advanced → Go to Life Tracker*. The `drive.appdata` scope is low-risk and does **not** require Google's verification process.

### 4. Create the OAuth Client ID

1. <https://console.cloud.google.com/apis/credentials>
2. **+ CREATE CREDENTIALS** → **OAuth client ID**.
3. Application type: **Web application**.
4. **Authorized JavaScript origins** — add every origin you will load the app from. Examples:
   ```
   http://localhost:8080
   https://YOUR-USERNAME.github.io
   ```
   The path doesn't matter, only the **scheme + host + port**. Add `http://127.0.0.1:8080` too if you tend to use that instead of `localhost`.
5. **Create** → copy the generated Client ID (ends in `.apps.googleusercontent.com`).

### 5. Tell the app about your Client ID

Three options — pick one:

| Option | When to use |
|---|---|
| Edit `DEFAULT_CLIENT_ID` in `index.html` (top of `<script>`) | You're forking and publishing your own copy. |
| Append `?client_id=YOUR-ID.apps.googleusercontent.com` to the URL | One-off / quick testing. |
| Click **⚠️ Setup cloud sync** in the toolbar → paste your ID → **Save & use** | Per-browser override (kept in `localStorage`). Friendly for forkers visiting your hosted copy. |

Then click **🔑 Google Drive** → consent → done. Data auto-saves to Drive 2.5 seconds after each edit, and auto-loads on every device you sign in on.

---

## Local development

Drive sync requires the file to be served from an `http(s)` origin (Google blocks `file://`). The simplest way:

```pwsh
# from the repo root
python -m http.server 8080
# then open http://localhost:8080/
```

No build, no install, no watchers.

---

## Publishing to GitHub Pages

1. Push the repo to GitHub.
2. **Settings → Pages**:
   - Source: `Deploy from a branch`
   - Branch: `main`, Folder: `/ (root)`
   - Save.
3. Wait 1–2 min for the first deploy. URL will be:
   - User site: `https://YOUR-USERNAME.github.io/`
   - Project site: `https://YOUR-USERNAME.github.io/REPO-NAME/`
4. **Important:** add that exact origin (no path) to **Authorized JavaScript origins** in your OAuth Client ID — otherwise you'll get `Error 400: origin_mismatch`.

### Is it safe to commit the Client ID?

**Yes.** The OAuth 2.0 Client ID is a public identifier — every web app exposes its own. The thing protecting it from abuse is the **Authorized JavaScript origins** list (only your registered origins can use it).

**Never** commit a Client *Secret*. This app's flow (GIS implicit) does not use one, so you are safe.

---

## Troubleshooting

| Error | Fix |
|---|---|
| `Access blocked: doesn't comply with OAuth 2.0 policy` | You opened `file:///…` directly. Serve over `http://localhost:8080` instead. |
| `Error 400: invalid_request` | Same as above, or the Client ID doesn't exist / is malformed. |
| `Error 400: origin_mismatch` | Add the exact `scheme://host:port` from your browser address bar to **Authorized JavaScript origins** in the Cloud Console. Wait ~60 s. |
| `Error 403: access_denied — has not completed verification` | Add your Google account under **Test users** on the OAuth consent screen. |
| Sync dot turns red after ~1 hour of use | OAuth access tokens expire hourly. The app auto-refreshes silently. If it doesn't, click **🔑 Google Drive** to re-sign in. |
| Sync stops working in Testing mode after 7 days | Test-mode refresh tokens expire weekly. Sign in again — your data is intact. |

---

## Data & privacy

- All data is stored in `localStorage` under the key `lft_v7` and (optionally) in `life-tracker-data.json` inside your Google Drive's **app data folder** — a hidden folder only this app can read or write to.
- No analytics, no telemetry, no third-party trackers. The only external calls are to `accounts.google.com` (sign-in) and `www.googleapis.com` (Drive REST). Open DevTools → Network and verify.

---

## License

[MIT](./LICENSE) © 2026 [Dipak Shaw](https://github.com/dipak-shaw) — do whatever you want, just don't sue.

## Credits

- **Concept, design, and code:** [Dipak Shaw](https://github.com/dipak-shaw)
- **Sync engine:** Google Identity Services + Google Drive REST API
- **Hosting:** GitHub Pages (free tier)

If you find this useful, a star on the repo means a lot ⭐.
