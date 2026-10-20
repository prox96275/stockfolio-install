# Stockfolio

Self-hosted investment tracking with Australian tax reporting. Stockfolio runs as one small Docker container with a web
app you can also install on your phone. Your data stays in a single folder on your own machine.

## Features

**Portfolio tracking**
- ASX, US and other global shares, ETFs, crypto (e.g. `BTC-AUD`) and manually priced assets
- Automatic prices from your choice of data source, with foreign holdings converted to AUD
- Holdings show value, cost base, unrealised and realised gains, income, total return, and an annualised money-weighted return (IRR)
- Chart of portfolio value against money invested
- Several portfolios (you, partner, family trust, SMSF, company), each with its own tax settings
- Net worth: cash, property, super and vehicles, minus mortgages, loans and credit cards, with history

**Australian tax**
- Financial-year reporting from 1 July to 30 June
- CGT parcel tracking with FIFO, LIFO, *Minimise gain* or *Maximise gain* parcel matching
- 12-month CGT discount: 50% for individuals and trusts, 33⅓% for SMSFs, none for companies
- Capital losses are applied to non-discount gains first, and unused losses carry forward each year. You can also enter opening losses from earlier years.
- Brokerage goes into the cost base and reduces proceeds. Foreign trades are converted at the FX rate on the trade date.
- Share splits and consolidations, cost base adjustments, and AMIT/AMMA net cost base adjustments, including CGT event E10
- Dividends: franked, unfranked, franking credits (auto-calculated at 30%), TFN withholding, foreign income and foreign tax (FITO)
- Trust/ETF distributions: other income, discounted capital gains (grossed up ×2), other-method gains and tax-deferred amounts, assigned to the FY of the ex/record date
- DRP: entering DRP units automatically creates the new purchase parcel
- **Find missing dividends**: checks dividend history against what you held on each ex-date
- **Tax report per FY**: CGT event schedule, net capital gain (18A), losses carried forward (18V), and income totals mapped to individual return labels (10L, 11S/T/U/V, 13U/C/Q/R, 20E/M/O). You can print it, save it as PDF or export CSV.

**Record keeping**
- Attach contract notes and statements to each trade or dividend, or upload general documents by FY
- **Smart import**: reads CommSec and Stake contract notes (PDF) and exports (CSV) into trades for you to review
- Full audit log of every create, change and delete, including the before and after record
- Deleted documents are moved to `deleted-attachments/` instead of being destroyed
- Automatic daily zip backup of the database and documents, plus a one-click download
- CSV import (with Sharesight "All trades" export support) and CSV export of everything

## Requirements
Any machine that runs **Docker with Docker Compose** and stays on while you want to use the app. Examples:
- a Linux server or mini PC
- a NAS with container support
- Windows or Mac with Docker Desktop
- a Raspberry Pi 4/5 (64-bit OS)

You need about 200 MB of disk plus your documents, and 512 MB of RAM. Images are provided for x86-64 and ARM64.

## Install
1. Create an empty folder, for example `stockfolio`, and put `docker-compose.yml` in it.
2. Optional: edit the file.
   - Change the port `8085` if something else already uses it.
   - Set `PUID`/`PGID` to the user that should own the `data/` folder. Run `id` on the host to see your numbers.
   - Set `TZ` to your timezone.
3. Start it, from a terminal in that folder:
   ```bash
   docker compose up -d
   ```
   Or import the compose file into your Docker app (Portainer, a NAS container manager, etc.).
4. **First sign-in.** Read the container log:
   ```bash
   docker logs stockfolio
   ```
   Or use the Logs view in your Docker app. Find the box headed **FIRST SIGN-IN**; it shows the username `admin` and a **one-time password**. Open `http://<server-address>:8085`, sign in with them, and **choose your own username and password** (12+ characters). The one-time password stops working at that point. No password is ever stored in a file.
5. **Turn on two-factor login.** Go to **Settings → Security → Set up two-factor login**, scan the QR code with any authenticator app, and enter the code.

## First steps
1. **Settings → Portfolios**: set the entity type, the parcel method and any capital losses carried forward.
2. **Settings → Market data**: choose where prices come from (see below).
3. Add trades (**Trades → + Add trade**), drop broker documents into **Smart import**, or import a CSV (**Import / export**; templates are provided). ASX codes end in `.AX`, e.g. `BHP.AX`, `VAS.AX`.
4. Add dividends from your statements, or use **Find missing dividends**. For ETFs, enter the annual AMMA statement as a *Distribution*.
5. At tax time, open **Tax reports**, pick the FY, and print or save it as PDF for your accountant.

## Market data
Choose a price source in **Settings → Market data**:
- **Yahoo Finance:** free, no key needed, but unofficial and for personal use only.
- **EODHD:** licensed prices, history, dividends (with payment dates) and FX for the ASX and global markets. You need your own API key from eodhd.com; make sure your plan covers the exchanges you hold. **Test connection** checks the key.
- **Manual prices only:** no internet look-ups. Set prices under **Holdings → Edit / price** and enter FX rates on foreign trades.

## Plans and licence keys
| Plan | Portfolios | Holdings | Licence |
|---|---|---|---|
| Free | 1 | 15 | no key needed |
| Personal | 2 | unlimited | yearly key |
| Family / SMSF | 5 | unlimited | yearly key |

To activate a plan, paste the key into **Settings → Licence**. Keys are checked offline, with no contact with any server.

- **Yearly renewal:** each key is valid for one year, and the Licence card shows the date.
  - From 30 days before that date, the dashboard reminds you to renew.
  - After the date there are 14 days' grace.
  - After that, the app switches to the Free plan until you enter your renewal key.
  - Renewal keys start the day your current one ends, so renewing early never loses days.
- **Your data is never locked or deleted.** Over a limit, or after expiry, you can still view, edit, sell and export everything; you just can't add more portfolios or holdings.

## Phone app (iPhone & Android)
Stockfolio can be installed on a phone as an app: its own icon, full-screen, with a bottom tab bar. It always talks to your own server, and financial data is never stored on the phone.

Phones only allow a full app install from an **HTTPS** address. How you provide one is up to you. Common options:
- a reverse proxy with a TLS certificate
- a private network / VPN service that issues HTTPS names
- a secure tunnel

Point it at `http://<server>:8085`. Then:
- **iPhone:** open the HTTPS address in **Safari**, tap **Share → Add to Home Screen**.
- **Android:** open it in **Chrome**, tap **Install app**, or go to **Settings → Phone app → Install app** in Stockfolio.

On a plain `http://` address you can still add a home-screen shortcut; it opens like a web page.

## Security
- **Keep it private.** Don't expose port 8085 directly to the internet. For remote access, put it behind HTTPS, and ideally also a VPN or other access control of your choice.
- Built-in protections:
  - login lockout after 5 wrong attempts (15 minutes), plus a delay on every failure
  - optional authenticator-app two-factor login, with replayed codes rejected
  - strict same-site session cookies and a block on cross-site requests
  - browser security headers (CSP, no framing, nosniff)
  - uploaded files can't run script in the app
  - every sign-in and failed attempt recorded in the audit log
- Container hardening:
  - runs as a normal user (`PUID`), not root
  - read-only filesystem, all Linux capabilities dropped
  - `no-new-privileges`, plus memory and process limits
- **Passwords** exist only as one-way scrypt hashes in the database. Change yours in **Settings → Security → Change username / password**; doing so signs out every other device.
- **Forgot your login?** In `docker-compose.yml`, remove the `#` in front of `RESET_PASSWORD: "1"`, then run `docker compose up -d`. The log shows a new one-time password. Sign in, choose a new login, then put the `#` back and run `docker compose up -d` again.
- **Lost your authenticator device?** Do the same with `RESET_2FA: "1"`.
- If **every** access is over HTTPS, set `COOKIE_SECURE: "1"`.

## Backups
Everything is in the `data/` folder next to your compose file:
```
data/portfolio.db        # all records
data/attachments/FYxxxx  # uploaded documents
data/backups/            # automatic daily zips + pre-upgrade copies
```
Include `data/` in your normal backups. **Import / export → Download full backup** gives you a zip at any time.

To restore: stop the container, unzip a backup into `data/`, and start it again.

## Updating
```bash
docker compose pull
docker compose up -d
```
Before a new version changes the database, it saves a copy to `data/backups/pre-upgrade-…db` and then upgrades automatically. A newer database is never opened by an older version. Pin a version tag in the compose file (e.g. `:1.2`) if you prefer to choose when to upgrade.

## Configuration (environment variables)
| Variable | Default | |
|---|---|---|
| `PUID` / `PGID` | 1000 / 1000 | Owner of the `data/` folder; the app runs as this user |
| `TZ` | Australia/Sydney | Timezone |
| `RESET_PASSWORD` | *(off)* | `"1"` issues a new one-time password at start-up |
| `RESET_2FA` | *(off)* | `"1"` turns off two-factor at start-up |
| `SESSION_DAYS` | 7 | How long a sign-in lasts |
| `COOKIE_SECURE` | 0 | `1` only if every access is over HTTPS |
| `ALLOWED_HOSTS` | *(empty)* | Extra host names for very old browsers behind a proxy, comma separated |
| `LOGIN_MAX_ATTEMPTS` / `LOGIN_LOCK_MINUTES` | 5 / 15 | Lockout policy |
| `PRICE_REFRESH_MINUTES` | 60 | Price refresh interval |
| `BACKUP_KEEP` | 30 | Daily backups to keep (0 disables) |
| `MAX_UPLOAD_MB` | 25 | Max document size |
| `SECRET_KEY` | auto-generated in `data/` | Session signing key |
| `UPDATE_URL` | Stockfolio release feed | Where the app checks once a day for a newer version (a plain download - nothing about you is sent). Set to `""` to turn off. |

## Smart import (CommSec & Stake)
Open **Smart import**, choose the portfolio, and drop in:
- **CommSec**:
  - contract notes as PDFs (Portfolio → Accounts → Confirmations)
  - the transactions CSV (Portfolio → Transactions → Download)
- **Stake** (AUS/ASX and Wall St/USD):
  - trade confirmation PDFs
  - the activity/trade history CSV export

Each trade appears in a review table, with anything uncertain highlighted and likely duplicates unticked. Fix anything wrong, then click **Save selected trades**. Each PDF is stored as a tax record and linked to its trade.

Only text PDFs from the broker can be read, not scans or photos. Each PDF is read as one trade.

## Checking an installation
```bash
docker exec stockfolio python -m tests.test_tax
docker exec stockfolio python -m tests.test_security
docker exec stockfolio python -m tests.test_parsers
docker exec stockfolio python -m tests.test_db
docker exec stockfolio python -m tests.test_e2e   # full security + feature check on a throw-away copy
```

## Notes and limitations
- This is general information, not tax advice. Check the reports against broker and registry statements, or have your tax agent review them.
- Not covered automatically: takeovers and demergers (enter these as a sell plus a buy, or as cost base adjustments with the issuer's apportionment), options, CGT rollovers, small-business concessions and indexation.
- Never store your TFN or bank passwords in the app.
