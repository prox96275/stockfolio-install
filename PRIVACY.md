# Stockfolio privacy note

**Short version:** Stockfolio runs on your own computer. Your portfolio, tax records, documents and login stay there.
We, the seller, never receive them and can't see them.

## What stays on your computer
Everything you enter or upload is stored only in the `data/` folder of your installation:
- trades, dividends, holdings, net-worth items and tax settings
- uploaded contract notes and statements
- your username, a one-way hash of your password, and your two-factor secret
- the audit log and backups
- your licence key

Nothing in that folder is sent to us. There's no analytics, tracking, advertising or crash reporting.

## What Stockfolio downloads
Stockfolio downloads a few things from the internet. These are downloads: none of them sends your records, name,
email or licence key.

| What | From | When | What the other side sees |
|---|---|---|---|
| Share prices, price history, dividends, exchange rates | The price source you choose in **Settings → Market data** (Yahoo Finance or EODHD; none for *Manual prices*) | Hourly by default, and when you open charts | The share codes you hold or search for, your internet address, and your EODHD API key if you use EODHD |
| Consumer Price Index (for the 2027 CGT rules) | Australian Bureau of Statistics | Weekly | Your internet address |
| Update file (`latest.json`) | GitHub, from the Stockfolio install repository | Once a day | Your internet address |

The update file tells Stockfolio whether a newer version exists. It also carries a list of cancelled licence key
ids, signed by us. Your Stockfolio checks that list on your computer: **your key is never sent anywhere.** To turn the
update check off, set `UPDATE_URL: ""` in `docker-compose.yml`.

Those services (Yahoo, EODHD, the ABS, GitHub) handle your internet address under their own privacy policies.

## What we hold as the seller
When you buy a licence, we keep:
- your name (it's written into your key and shown in Stockfolio)
- your email address, if you give one
- the plan, the dates, and the key's id

We use these only to issue, renew and support your licence, and for tax and accounting records. Payments are
handled by our payment provider under its own privacy policy; we don't see your card details. We don't sell or share
your details, except where the law requires it.

You can ask what we hold about you, or ask us to correct it, using the contact details on your purchase receipt.

## Your part
- Keep your installation private (see **Security** in the README). Anyone who can sign in to it can see your records.
- Back up your `data/` folder. We can't recover it for you, because we never have a copy.
