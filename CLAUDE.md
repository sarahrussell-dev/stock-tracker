# Stock Tracker — Claude Code Context

## What this is
A personal stock tracker and AI advisor web app. Single HTML file deployed via GitHub Pages.

**Live URL:** https://sarahrussell-dev.github.io/stock-tracker/
**Repo:** https://github.com/sarahrussell-dev/stock-tracker

## Tech stack
- **Frontend:** Single `index.html` — vanilla JS, no framework, no build step
- **Auth + DB:** Supabase (qqxzcfnmqqprcwckrlyw.supabase.co)
- **Market data:** Finnhub API via Supabase Edge Function proxy (`finnhub-proxy`)
- **AI:** Claude via Supabase Edge Function proxy (`claude-proxy`)
- **Background scans:** 9 Supabase Edge Functions (`market-scan-1` through `market-scan-9`)
- **Scheduling:** Supabase cron jobs — 9:40am, 1pm, 4:10pm ET weekdays

## Database tables
- `portfolios` — user positions (sym, shares, avg_cost, user_id). RLS on.
- `watchlists` — user watchlist (sym, user_id). RLS on.
- `scan_results` — stock scores (sym, data jsonb, updated_at). Public read.

## Scoring model (v2 — 6 factors)
Weights: Analyst 25%, Valuation 20%, Growth 20%, Profitability 15%, Momentum 10%, Health 10%
- Score range 0–100. Strong Buy ≥80, Buy 60–79, Hold 40–59, Sell 20–39, Strong Sell <20
- Missing metrics skip gracefully (weight redistributed)
- Same logic in both frontend `computeCompositeScore()` and all 9 edge functions

## Key Supabase config
- Anon key is safe to be public (by design)
- Service role key is in Supabase Edge Function secrets only
- Finnhub API key is in Edge Function secrets only
- Row limit set to 1000 via `alter role authenticator set pgrst.db_max_rows = 1000`

## Stock universe
260 stocks — S&P 500 large caps + high-growth names across 12 sectors.
Split across 9 edge function chunks of ~29 stocks each.

## How to make changes
1. Edit `index.html` directly — it's the entire frontend
2. Deploy by committing and pushing to GitHub (Pages auto-deploys)
3. Edge functions are deployed via Supabase CLI or dashboard
4. Always run a JS syntax check before deploying HTML

## Current version
Index 36 — deployed June 2026.
All features: session persistence, 6-factor scoring, personalized news tab,
AI advisor with portfolio context + ranking queries, + Watch confirmation button.

## Users
- Sarah (developer, sarahrussell.dev)
- Sarah's dad (end user)
