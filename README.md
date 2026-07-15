# Printway Packaging ERP

A lightweight, single-file ERP web app for tracking purchase and sales ledgers with running account balances — built for a packaging & printing business, with staff authentication and secure row-level data access.

## Overview

Most small manufacturing/trading businesses track supplier and customer balances in paper ledgers or spreadsheets. This app replaces that with a simple, real-time web dashboard: add a client, log transactions (quantity × rate), and the running balance (due / advance) updates automatically — with PDF export and print-ready statements.

## Features

- 🔐 **Staff authentication** — Supabase Auth email/password login; no public access to data
- 📦 **Two ledgers** — separate Purchase (suppliers) and Sales (buyers) client lists
- 🧮 **Automatic running balance** — `balance = previous balance + (qty × rate) - paid`, carried row to row per client
- ✏️ **Full CRUD** — add/edit/delete both clients and ledger entries
- 🧾 **PDF export & print** — generate a clean statement PDF per client, or print directly
- 📱 **Responsive** — usable on desktop and mobile
- 🔒 **Row Level Security** — Postgres RLS policies restrict all reads/writes to authenticated users only

## Tech Stack

- Vanilla JavaScript (ES modules) — no framework, no build step
- [Supabase](https://supabase.com) — Postgres database, Auth, and RLS
- [jsPDF](https://github.com/parallax/jsPDF) + `jspdf-autotable` — client-side PDF generation
- Plain CSS (custom properties / dark theme)

## Architecture

```
Browser (index.html)
   │
   │  Supabase JS client (auth + queries)
   ▼
Supabase Auth  ──────────────►  Postgres (clients, ledger_entries)
                                  RLS: authenticated-only policies
```

All business logic (balance calculation, sector switching, CRUD) runs client-side; Supabase handles auth, storage, and enforces access control at the database layer via RLS — no custom backend server required.

## Database Schema

Two tables — see [`setup.sql`](./setup.sql):

- **clients** — company profile, opening balance, sector (`purchase`/`sales`)
- **ledger_entries** — one row per transaction, linked to a client via foreign key with cascade delete

## Running Locally

1. Create a free project at [supabase.com](https://supabase.com)
2. Run [`setup.sql`](./setup.sql) in the Supabase SQL Editor (creates tables + RLS policies)
3. Add a staff login under **Authentication → Users**
4. Copy `config.example.js` → `config.js` and fill in your project's URL + anon/publishable key (found under **Settings → API Keys**)
5. Open `index.html` in a browser (or serve it with any static server)

```bash
cp config.example.js config.js
# edit config.js with your Supabase credentials
python3 -m http.server 8000
```

> `config.js` is git-ignored — your credentials never get committed.

## Security Notes

- Row Level Security is **enabled** on every table; only `authenticated` Supabase Auth users can read or write.
- The anon/publishable key is safe to expose client-side by design — actual protection comes from RLS policies, not key secrecy.
- No `service_role`/secret key is ever used in this client-side app.

## License

MIT — feel free to adapt this for your own use case.
