# Trading Dashboard — Plan Document

_Last updated after Round 5 of requirements gathering._

---

## Overview

A personal trading dashboard ("My Tradervue") built with **React + Tailwind CSS**, backed by **Google Sheets**, featuring **TradingView chart embeds**. Updated daily with trades, opportunities, and journal notes. Dark theme, clean and minimal.

---

## Stack

| Layer | Choice | Reason |
|---|---|---|
| Frontend | React + Tailwind CSS | Component-based, good charting library support |
| Backend / Storage | Google Sheets (one master sheet) | No server needed, easy to edit manually, accessible from anywhere |
| Charts | TradingView embed widgets | Interactive, live data, matches the user's existing TradingView workflow |
| Images | Uploaded screenshots (base64 or hosted URL) | For Playbook pattern images |

### Google Sheets Structure (one master Sheet)

| Tab Name | Contents |
|---|---|
| `Trades` | All daily trade log entries |
| `Watchlist` | Saved pre-market watchlist stocks |
| `Opps` | Daily best opps / top mover log |
| `Playbook` | Pattern catalog entries |
| `PreMarket` | Daily pre-market bias and notes |

---

## Instruments

- **Stocks / Equities only** (US market, standard shares)
- Account size: **under $10k**

---

## Tabs / Pages

### 1. Daily Trade Log

**Purpose:** Log every trade taken each day.

**Fields per trade:**
- Date (auto-set to today)
- Ticker symbol
- Entry price
- Exit price
- Share size
- P&L (auto-calculated: `(exit - entry) × size`)
- Entry time
- Exit time
- Setup / Pattern tag (dropdown linked to Playbook)
- Notes (freeform text)

**Features:**
- Inline mini TradingView chart per trade row (symbol auto-populated from ticker)
- Add trade form (manual entry)
- Edit / delete existing trades
- Color-coded P&L (green positive, red negative)
- Daily P&L summary bar at the top

---

### 2. Best Opps of the Day

**Purpose:** Track top movers and market opportunities — stocks that made big moves each day, whether traded or not.

**Workflow:**
1. Pre-market: Build watchlist (6–10 stocks) from saved list
2. During/after market: Flag which ones moved and add notes

**Fields per Opp:**
- Date
- Ticker
- % move (e.g. +8.4%)
- Direction (long / short opportunity)
- Setup / pattern that played out
- Notes on why it was notable
- Mini TradingView chart embed

**Watchlist feature:**
- Saved list of go-to watchlist stocks
- Each day, select from saved list or add new tickers
- Mark each as "watched," "traded," or "missed"

---

### 3. Performance / P&L

**Purpose:** Running analytics across all logged trades.

**Metrics displayed:**
- Daily / Weekly / Monthly P&L (running totals)
- Win rate (%)
- Average R-multiple (risk/reward) — requires stop price field (to be confirmed)
- Best trade (largest $ winner)
- Worst trade (largest $ loser)
- Equity curve chart (line chart of cumulative P&L over time)
- Breakdown by setup/pattern (which setups are most profitable)

**Open question:** Do you track a stop price per trade for R-multiple calculation? (Will ask in next round.)

---

### 4. Patterns / Playbook

**Purpose:** Personal catalog of trading setups with rules, images, and linked performance stats.

**Pre-loaded patterns (from your PineScript strategy):**

| Pattern | Description |
|---|---|
| Value Area Long (VAL) | Buy below VAL when Stochastic RSI < threshold |
| POC Reclaim | Price reclaims the Point of Control |
| VAH Breakout | Price breaks above Value Area High |
| LOWBB | Price drops below Lower Bollinger Band threshold |
| HIGHBB | Price spikes above Upper Bollinger Band threshold |

**Fields per pattern:**
- Name
- Category / type
- Description / rules (text)
- Entry criteria
- Exit criteria
- Chart screenshot upload (image)
- TradingView embed for a reference chart
- Auto-linked stats: # of times traded, win rate, avg P&L when this tag is used

---

### 5. Pre-Market Bias & Notes

**Purpose:** Daily morning section to set the trading plan before the open.

**Fields:**
- Date
- Market bias (Bullish / Bearish / Neutral / No trade)
- Key levels to watch (text or table of price levels)
- Catalysts / news notes
- Watchlist for the day (links to Best Opps tab)
- Freeform notes

---

### 6. Weekly / Monthly Review

**Purpose:** Auto-aggregated summary of performance over the week or month.

**Contents:**
- Weekly and monthly P&L totals
- Win rate breakdown by week/month
- Best and worst trades of the period
- Setup performance breakdown
- Observations / notes for the period (freeform text)
- Auto-populated from trade log data — no manual entry needed

---

## Visual Style

- **Dark theme**, clean and minimal
- Inspired by TradingView / Tradervue aesthetics
- Green for positive P&L, red for negative, neutral grays for metadata
- Accent color: TBD (will ask user)
- Font: Modern monospace or sans-serif

---

## TradingView Chart Integration

- **Large chart:** One full-size interactive TradingView Advanced Chart widget (ticker switchable) shown prominently on the Trade Log and Best Opps tabs
- **Mini charts:** Small TradingView Mini Symbol Overview widget embedded inline with each trade row and each opp row
- No API key required for TradingView widgets (free public embed)

---

## Google Sheets Integration

- Connect via **Google Sheets API** (OAuth or Service Account)
- App reads/writes rows to the master sheet
- Each section maps to a dedicated sheet tab (see Stack section above)
- Reads on page load, writes on form submit
- Optimistic UI updates (show change immediately, sync in background)

---

## Open Questions (to resolve in next rounds)

1. **Accent color** — what color accent do you want beyond green/red? (Blue, gold, teal, white only?)
2. **R-multiple tracking** — do you log a stop price per trade so we can calculate risk/reward automatically?
3. **Navigation style** — sidebar nav or top tab bar?
4. **Daily reset behavior** — when you open the app each day, should it auto-navigate to today's log or show a summary/home screen?
5. **Image hosting for Playbook** — where should uploaded screenshots live? (Google Drive, localStorage as base64, or an image hosting service?)
6. **Authentication** — is this purely private/local or do you want a login so it's accessible from any device securely?

---

## Build Order (proposed, not started)

1. Project scaffolding (Create React App or Vite + Tailwind)
2. Google Sheets API connection layer
3. Navigation shell + dark theme
4. Daily Trade Log tab (core feature)
5. Performance / P&L tab
6. Best Opps tab + Watchlist
7. Pre-Market Bias tab
8. Patterns / Playbook tab
9. Weekly / Monthly Review tab
10. TradingView chart embeds
11. Polish, mobile responsiveness check

_Build does not start until user approves the plan and open questions are resolved._
