# BMS Ticket Monitor (Fresh Repo Edition)

Free, 24/7 cloud monitor that pings your Telegram the moment booking opens for **Avengers: Doomsday** and **Dune: Part Three** in Hyderabad — filtered to specific theatres and formats.

This is a **fresh repo** setup (not a fork) to ensure GitHub Actions scheduled runs activate reliably. Forks can take 24-48 hours for cron schedules to be recognized; fresh repos activate within ~30 minutes.

## What it does

Every 5 minutes, GitHub Actions runs a Python script that:
1. Asks BookMyShow's API: "What's the current booking status?"
2. Compares to what it saw 5 minutes ago (state stored in repo)
3. If anything changed, sends you a Telegram push with a 1-tap booking link

## Alerts you'll get

- 🔓 **THEATRE UNBLOCKED** — a theatre just started selling tickets, OR released blocked inventory
- 📅 **NEW DATE OPENED** — a new date became bookable
- 🆕 **NEW SHOW** — a new showtime was added
- 🟢/🟠/🟡 **BACK IN STOCK** — a sold-out show became available again

Each alert includes a 1-tap **TAP TO BOOK NOW** link straight to the booking page. Theatre-unblock and new-date alerts are marked **high-urgency** (🚨 BOOK NOW!).

## Files in this bundle

| File | Purpose |
|---|---|
| `main.py` | The monitor script with Telegram push, cookie warming, filter groups |
| `.github/workflows/monitor.yml` | 5-min cron workflow, matrix for 2 movies, commit-back state |
| `pyproject.toml` | Python 3.11+ requirement, `requests` dependency |
| `.python-version` | Pins Python 3.12 for CI |
| `bms_state_avengers.json` | Empty baseline file (auto-populated on first run) |
| `bms_state_dune.json` | Empty baseline file (auto-populated on first run) |
| `.gitignore` | Standard Python ignores |
| `setup-guide.md` | Full step-by-step setup instructions |
| `README.md` | This file |

## Quick Start

Read **[setup-guide.md](./setup-guide.md)** for the full walkthrough (~20 min). TL;DR:

1. Create a NEW GitHub repo (don't fork) — name it e.g. `bms-ticket-monitor`
2. Upload all files from this bundle to the repo
3. Create a Telegram bot via @BotFather, get your chat ID
4. Create a GitHub Personal Access Token (with `repo` scope)
5. Add 3 secrets: `TELEGRAM_BOT_TOKEN`, `TELEGRAM_CHAT_ID`, `PAT_TOKEN`
6. Add 3 variables: `BMS_URL_AVENGERS`, `BMS_URL_DUNE`, `BMS_FILTER_GROUPS`
7. Trigger the workflow once manually to seed baselines
8. Done — within ~30 min, scheduled runs will kick in automatically

## Your configured filter

`BMS_FILTER_GROUPS` = `Prasads:PCX,AMB:*,Allu:*`

This means: only alert you when a show matches ANY of:
- **PCX format** at Prasads Multiplex (Masab Tank)
- **Any format** at AMB Cinemas (Sarath City Capital Mall)
- **Any format** at Allu Cinemas (Kokapet)

Both movies (Avengers + Dune) use the same filter rules.

## Cost

Free. Keep your repo **public** for unlimited GitHub Actions minutes.

## Customizing later

- **Add a third movie**: Add an entry to the matrix in `monitor.yml`, add a `BMS_URL_<NAME>` variable, commit an empty `bms_state_<name>.json`. Done.
- **Change filter**: Edit the `BMS_FILTER_GROUPS` variable — no code changes needed. Empty both state files to `{}` after changing filters so the baseline resets.
- **Different city**: Change the `BMS_URL_<NAME>` variable to a different city URL (Mumbai, Chennai, Bengaluru, etc. all work — region is auto-detected).
