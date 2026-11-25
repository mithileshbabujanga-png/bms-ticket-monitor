# Setup Guide — Fresh Repo Edition

This guide walks you through setting up a free, 24/7 cloud monitor that pings your phone on Telegram the moment booking opens for **Avengers: Doomsday** and **Dune: Part Three** in Hyderabad — at your chosen theatres/formats.

**Total setup time:** ~20 minutes
**Cost:** Free
**Latency:** Worst case ~5 min (GitHub's schedule minimum), best case ~30 sec

This is the **fresh repo** approach — not a fork. Fresh repos activate GitHub Actions schedules within ~30 min, vs forks which can take 24-48 hours.

---

## How it works (30-second version)

Every 5 minutes, GitHub Actions runs a Python script that:
1. Asks BookMyShow's API "what's the current booking status?"
2. Compares the answer to what it saw 5 minutes ago (stored in a JSON file committed to your repo)
3. If anything changed (date opened, new show, sold-out → available, theatre unblocked), it sends you a Telegram push with a 1-tap booking link

---

## What you'll need

- A GitHub account (free)
- A Telegram account (free)
- The BookMyShow URLs (provided below — no need to look them up)
- The 3 secrets from your previous setup: `TELEGRAM_BOT_TOKEN`, `TELEGRAM_CHAT_ID`, `PAT_TOKEN` (you can reuse the same ones — see Step 5)

That's it. No payment, no server, no laptop.

---

## Step 1 — Create a NEW GitHub repository (not a fork)

1. Go to https://github.com/new
2. Fill in:
   - **Repository name:** `bms-ticket-monitor` (or any name you like — keep it lowercase, no spaces)
   - **Description:** `Telegram monitor for Avengers: Doomsday and Dune: Part Three booking`
   - **Visibility:** **Public** (required for free unlimited GitHub Actions minutes)
   - **Initialize this repository with:** ✅ check **"Add a README file"**
3. Click **Create repository**

You now have a fresh empty repo at `https://github.com/<your-username>/bms-ticket-monitor`.

> **Why not fork?** Forks inherit the original repo's scheduler state, which can delay cron activation by 24-48 hours. Fresh repos get scheduled within ~30 min.

---

## Step 2 — Upload all files from this bundle

You need to upload 8 files. Do them one at a time using GitHub's web UI.

### 2.1 Upload `main.py`

1. In your new repo, click **Add file → Upload files**
2. Drag `main.py` from this bundle into the upload area
3. Scroll down → **Commit changes** → "Commit directly to main" → **Commit changes**

### 2.2 Upload `pyproject.toml`

Same process — drag `pyproject.toml`, commit.

### 2.3 Upload `.python-version`

Same process. (This file starts with a dot — GitHub might warn you, but it's fine.)

### 2.4 Upload `.gitignore`

Same process.

### 2.5 Upload `bms_state_avengers.json`

Same process. File contents should just be `{}`.

### 2.6 Upload `bms_state_dune.json`

Same process. File contents should just be `{}`.

### 2.7 Upload the workflow file — IMPORTANT: create the folder structure

This is the trickiest one because it needs to go inside `.github/workflows/`:

1. In your new repo, click **Add file → Create new file**
2. In the filename box, type: `.github/workflows/monitor.yml`
   - (Typing the slashes will auto-create the folders)
3. Open `monitor.yml` from this bundle in a text editor, copy ALL contents
4. Paste into the GitHub editor
5. Scroll down → **Commit changes** → "Commit directly to main" → **Commit changes**

### 2.8 Upload `README.md` (optional — replaces the auto-generated one)

1. In your new repo, click on `README.md`
2. Click the pencil icon (Edit)
3. Delete everything, paste the contents of `README.md` from this bundle
4. Commit

### Verify

After all uploads, your repo should contain:
```
.github/
  workflows/
    monitor.yml
.gitignore
.python-version
README.md
bms_state_avengers.json
bms_state_dune.json
main.py
pyproject.toml
```

---

## Step 3 — Create a Telegram bot (skip if you already have one)

> **⚠️ SECURITY:** The token BotFather gives you is a secret credential. **NEVER paste it into any chat, AI assistant, forum, or commit it to a repo.** Treat it like a password. If you accidentally leak it, run `/revoke` in @BotFather immediately.

If you already have `@doomsday_avngrs_bot` from your previous setup, you can **reuse it** — skip to Step 4 and reuse your existing token + chat ID.

To create a new bot:
1. Open Telegram, search for **@BotFather**
2. Send: `/newbot`
3. Follow prompts — name it anything, username must end in `bot`
4. Copy the **API token** to a password manager

---

## Step 4 — Get your Telegram chat ID (skip if you already have one)

> **⚠️ SECURITY:** Your chat ID is also sensitive. Treat it as private.

1. Send any message (e.g. "hi") to your bot in Telegram
2. Open this URL in your browser (replace `<BOT_TOKEN>`):
   ```
   https://api.telegram.org/bot<BOT_TOKEN>/getUpdates
   ```
3. Look for `"chat":{"id":XXXXXXXXX}` — that number is your **chat ID**
4. If you see `{"result":[]}`, you forgot step 1 — send "hi" to the bot first, then refresh

---

## Step 5 — Create a GitHub Personal Access Token (PAT)

> **⚠️ SECURITY:** The PAT is a secret credential — anyone with it can push to your repo and modify your workflow. **Never paste it into chats or commit it.** If you leak it, immediately delete it at https://github.com/settings/tokens.

If you already have a PAT from your previous setup, you can **reuse it** — just make sure it's not expired.

To create a new PAT:
1. Go to https://github.com/settings/tokens
2. Click **Generate new token → Generate new token (classic)**
3. Note: `Ticket monitor state committer`
4. Expiration: **No expiration** (or 1 year)
5. Scopes: check **only** `repo`
6. Click **Generate token** at the bottom
7. **Copy the token** — it starts with `ghp_...`

---

## Step 6 — Add secrets to your new repo

In your new repo: **Settings → Secrets and variables → Actions → Secrets → New repository secret**

Add these 3 secrets:

| Secret name | Value |
|---|---|
| `TELEGRAM_BOT_TOKEN` | Your bot token (e.g. `8123456789:AAH-xxx...`) |
| `TELEGRAM_CHAT_ID` | Your chat ID (e.g. `5278943210`) |
| `PAT_TOKEN` | Your PAT (starts with `ghp_...`) |

> You can copy/paste the same values you used in the old repo. The secrets are local to this new repo, so they don't conflict.

---

## Step 7 — Add repository variables

In the same settings page, switch to the **Variables** tab → **New repository variable**.

Add these 3 variables:

| Variable name | Value |
|---|---|
| `BMS_URL_AVENGERS` | `https://in.bookmyshow.com/movies/hyderabad/avengers-doomsday/buytickets/ET00526306` |
| `BMS_URL_DUNE` | `https://in.bookmyshow.com/movies/hyderabad/dune-part-three/buytickets/ET00491771` |
| `BMS_FILTER_GROUPS` | `Prasads:PCX,AMB:*,Allu:*` |

### What `BMS_FILTER_GROUPS` means

`Prasads:PCX,AMB:*,Allu:*` translates to: "alert me when a show matches ANY of these groups":

- **PCX format** at Prasads Multiplex (the famous PCX screen)
- **Any format** at AMB Cinemas (Sarath City Capital Mall) — all screens
- **Any format** at Allu Cinemas (Kokapet) — all screens including Dolby Cinema

A show passes the filter if it matches ANY group. `*` means "any format at this theatre".

### Filter groups syntax (for future customization)

| `BMS_FILTER_GROUPS` value | Meaning |
|---|---|
| `Prasads:PCX,AMB:*,Allu:*` | PCX at Prasads, all formats at AMB and Allu (YOUR SETUP) |
| `Prasads:PCX,Prasads:IMAX,Allu:DOLBY CINEMA` | PCX or IMAX at Prasads, Dolby Cinema at Allu only |
| `PVR:*,Allu:*` | All formats at any PVR or Allu venue |
| `Prasads:*` | All formats at Prasads only |

**Common Hyderabad theatres:** `Prasads`, `AMB`, `Allu`, `PVR ICON`, `PVR Oberon`, `INOX`, `Cinepolis`, `Asian`

**Common BMS format strings:** `PCX`, `DOLBY CINEMA`, `IMAX`, `4DX`, `PXL`, `LASER 4K`, `HDR By Barco`

### Optional extra variables (leave unset for ASAP booking)

| Variable | Value | What it does |
|---|---|---|
| `BMS_TIME` | `evening,night` | Only alert for evening (4-7pm) and night (7pm-midnight) shows |
| `BMS_DATES` | `20261217,20261218` | Only check these specific dates (YYYYMMDD) |

---

## Step 8 — Run it for the first time

1. Go to your repo's **Actions** tab
2. If you see a yellow banner asking to enable workflows, click **I understand my workflows, go ahead and enable them**
3. In the left sidebar, click **Ticket Monitor** (note: this is the new workflow name — different from the old "BMS Ticket Checker")
4. Click **Run workflow → Run workflow**
5. Wait ~30 sec, refresh — you should see **2 jobs**: `check (avengers)` and `check (dune)`
6. Both should turn green ✅ within ~30 sec

**What you should see on your phone:** Two Telegram messages within ~1 minute:

**Message 1:**
```
✅ BMS Notifier is LIVE

🎬 Monitoring: Avengers: Doomsday
📍 URL: https://in.bookmyshow.com/movies/hyderabad/avengers-doomsday/...
🕒 Baseline stored at: 25 Nov 2026, ...

Active filters:
🏷️ Movie label: Avengers
🎯 Filter groups: Prasads:PCX,AMB:*,Allu:*

You'll get a push the moment booking opens...
👉 Current booking page: https://in.bookmyshow.com/...
```

**Message 2:** Same but with `Dune` label and Dune URL.

If you don't get these messages, see Troubleshooting below.

---

## Step 9 — Verify the schedule is active

Unlike forks (which can take 24-48 hours), fresh repos usually pick up cron schedules within 30 minutes. Here's how to verify:

1. **Actions** tab → **Ticket Monitor**
2. Wait 10 minutes
3. Refresh — you should see runs labeled **"scheduled"** (with a clock icon) appearing between your manual runs
4. Each scheduled run prints "No changes since last check." in the logs — that's correct

If after 30 min you still see only manual runs, see "Scheduled runs not firing" in Troubleshooting below.

---

## What the alerts look like

**When a theatre unblocks tickets (high-urgency):**
```
🚨 BOOK NOW! [Avengers] Avengers: Doomsday — 1 change(s)!

⚡ Changes:
• 🔓 THEATRE UNBLOCKED: Allu Cinemas — now selling tickets (8 show(s))

👉 TAP TO BOOK NOW:
https://in.bookmyshow.com/movies/hyderabad/avengers-doomsday/buytickets/ET00526306

🕒 Checked: 17 Dec 2026, 09:00 AM
```

**When booking opens for a new date (high-urgency):**
```
🚨 BOOK NOW! [Dune] Dune: Part Three — 1 change(s)!

⚡ Changes:
• 📅 NEW DATE OPENED: 20261218

👉 TAP TO BOOK NOW:
https://in.bookmyshow.com/movies/hyderabad/dune-part-three/buytickets/ET00491771

🕒 Checked: 18 Dec 2026, 09:00 AM
```

**When a new showtime is added:**
```
⚡ [Avengers] Avengers: Doomsday — 1 change(s)!

⚡ Changes:
• 🆕 NEW SHOW: Prasads Multiplex 07:50 PM [20261217] — PCX Rs.500

👉 TAP TO BOOK NOW:
https://in.bookmyshow.com/movies/hyderabad/avengers-doomsday/buytickets/ET00526306
```

**When a sold-out show comes back:**
```
⚡ [Avengers] Avengers: Doomsday — 1 change(s)!

⚡ Changes:
• 🟢 BACK IN STOCK: Allu Cinemas 09:00 PM [20261217] — DOLBY CINEMA -> AVAILABLE

👉 TAP TO BOOK NOW:
https://in.bookmyshow.com/movies/hyderabad/avengers-doomsday/buytickets/ET00526306
```

---

## Troubleshooting

### I didn't get the "BMS Notifier is LIVE" messages
1. **Actions** tab → click the latest run → expand "Run ticket monitor" step (for whichever job didn't message you)
2. Look for one of these:
   - `Telegram skipped — TELEGRAM_BOT_TOKEN or TELEGRAM_CHAT_ID not set.` → Your secrets are missing/typo'd. Re-check Step 6.
   - `Telegram error 401: Unauthorized` → Bot token is wrong.
   - `Telegram error 400: chat not found` → Chat ID is wrong, OR you never sent a message to the bot. Re-do Step 4.
   - `Invalid BMS_URL. Could not extract event/region.` → The `BMS_URL_AVENGERS` or `BMS_URL_DUNE` variable is missing or has a typo. Re-check Step 7.
   - `warmup: HTTP 403` then `HTTP 403` → BMS blocking GitHub's IP. Retry in 5 min; if persists >1 hour, see "BMS API returns 403" below.
3. Send any message to your bot in Telegram and re-run the workflow — Telegram bots can only message users who have started a conversation first.

### One job passes, one fails
- Click into the failed job → expand "Run ticket monitor" → look at the last 15 lines
- Common causes: wrong `BMS_URL_<NAME>` variable, BMS API rate-limiting one specific movie

### Scheduled runs not firing after 30 min
For fresh repos, this is rare but can happen. Try these in order:

1. **Make a trivial commit** to nudge the scheduler:
   - Edit `README.md` → add a comment line at the top → commit
   - Wait 10 min, refresh Actions page

2. **Star your own repo** (top-right Star button on repo page) — gives an activity signal

3. **Wait 1 hour total** — sometimes GitHub's scheduler is just slow on the first day

4. **Check workflow isn't disabled**: Actions tab → if you see "This scheduled workflow is disabled because there hasn't been activity in this repository for at least 60 days", click Enable

### BMS API returns 403 consistently
BMS sometimes blocks cloud provider IPs. The script already uses cookie warming + User-Agent rotation to bypass basic anti-bot checks, but if it's still 403'ing after multiple runs:

- **Wait 1 hour** — BMS blocks are usually temporary
- **Re-run the workflow** — GitHub spins up a new runner with a different IP each time, so a re-run often succeeds
- **Long-term fix** — if BMS is persistently blocking GitHub IPs, we'd need to switch to Cloudflare Workers (different IP range). Ping me for that variant.

### `bms_state_*.json` files aren't being committed
Check the "Commit state back" step logs. If it says "State file not found — checker didn't produce one", that means the script crashed before saving state. Look at the "Run ticket monitor" step above it for the actual error.

If it says "git push permission denied", your `PAT_TOKEN` secret is missing or doesn't have `repo` scope. Redo Step 5.

### Script prints "No showtimes found" repeatedly
This is **correct behavior** if booking hasn't opened yet. For:
- **Avengers: Doomsday** (releases Dec 17, 2026) — booking typically opens 7-14 days before release
- **Dune: Part Three** (releases Dec 18, 2026) — same window

The script will keep polling every 5 min. The moment booking opens, you'll get the alert.

---

## Maintenance

- **Once you've booked your tickets:** Disable the workflow (Actions → ⋯ → Disable workflow) so it stops pinging you. Don't delete the repo — reuse it for the next Marvel/Dune movie by changing the `BMS_URL_<NAME>` variables and emptying the state files to `{}`.
- **To add a third movie:**
  1. Add a new entry to the matrix in `monitor.yml` (copy the avengers/dune entry, change name/label/url_var/state_file)
  2. Add a `BMS_URL_<NEW>` variable
  3. Create an empty `bms_state_<new>.json` file with `{}`
  4. Commit, run workflow once to seed the new baseline
- **GitHub Actions free tier:** With 2 matrix jobs × 288 runs/day × ~30 sec/run = ~5 hours/day = ~150 hours/month. Keep your repo **public** for unlimited minutes.

---

## Cleaning up the old repo

Once the new repo is working, you can either:

1. **Disable** the old fork's workflow (Actions → ⋯ → Disable workflow) — keeps the repo around but stops it from running
2. **Delete** the old fork entirely (Settings → scroll to bottom → Danger Zone → Delete this repository)

Recommended: disable for now, delete after the new repo has been running reliably for a few days.

---

## Tips for actually getting tickets

1. **Enable Telegram notifications for this bot** — Telegram → tap bot name → ⋯ → Enable Notifications. Make sure your phone isn't on Do Not Disturb during peak hours.
2. **Save your BMS login + payment method** — Log into BookMyShow on your phone's browser now, save your card to your wallet. Complete one throwaway booking flow up to payment (then cancel) to warm up the session.
3. **Pre-select your city + format** — Bookmark the URL with your preferred format pre-selected.
4. **Have a backup plan** — Avengers/Dune openings sell out in 5-15 min for prime shows. Have a second-choice theatre and time ready.
5. **The alert link takes you to the movie page**, not directly to seat selection. From there: pick date → pick showtime → pick seats → pay. About 4 taps.

---

## Need help?

If you get stuck, reply with:
- What step you're on
- What you expected to happen
- What actually happened (paste exact error messages, but **never paste secrets/tokens**)
- A screenshot if it's a UI issue

Enjoy the movies!
