# Trading Dashboard — Plan Document

_Last updated after Round 7 of requirements gathering. All open questions resolved._

---

## Overview

A personal trading dashboard ("My Tradervue") built with **React + Tailwind CSS**, backed by **Google Sheets**, featuring **TradingView chart embeds**. Updated daily with trades, opportunities, and journal notes. Dark theme, clean and minimal. Single-machine use, no login required.

---

## Stack

| Layer | Choice |
|---|---|
| Frontend | React + Tailwind CSS (Vite) |
| Data Storage | Google Sheets — one master sheet, tabs per section |
| Charts | TradingView embed widgets (free public embeds, no API key) |
| Pattern Images | Google Drive (linked URLs stored in Sheets) |
| Auth | None — single machine, local use only |

### Google Sheets Tab Structure

| Tab | Contents |
|---|---|
| `Trades` | All daily trade log entries |
| `Watchlist` | Saved persistent watchlist stocks |
| `Opps` | Daily best opps / top mover log |
| `Playbook` | Pattern catalog entries |
| `PreMarket` | Daily pre-market bias and key levels |

---

## Instruments

- **Stocks / Equities only** (US market)
- Account size: **under $10k**

---

## Visual Design

- **Theme:** Dark, clean, minimal — TradingView / Tradervue aesthetic
- **P&L colors:** Green (positive), Red (negative)
- **Accent color:** Electric blue
- **Navigation:** Left sidebar with icons + labels
- **Default landing page:** Dashboard / summary home screen (shows yesterday's P&L, quick-add buttons, today's date)
- **Font:** Modern sans-serif (Inter or similar)

---

## Pages / Tabs

### Home (Dashboard)

Landing page on every open. Shows:
- Today's date and market day count
- Yesterday's P&L summary card
- This week's running P&L
- Quick-add button for a new trade
- Quick-add button for a new opp
- Link to today's pre-market notes (with a "not yet filled in" indicator)
- Most recent 3 trades (mini preview)

---

### 1. Daily Trade Log

**Purpose:** Log every trade taken.

**Fields per trade:**
| Field | Type |
|---|---|
| Date | Auto (today), editable |
| Ticker | Text input |
| Entry price | Number |
| Exit price | Number |
| Share size | Number |
| P&L | Auto-calculated: `(exit − entry) × size` |
| Entry time | Time picker |
| Exit time | Time picker |
| Setup / Pattern | Dropdown (pulls from Playbook) |
| Notes | Freeform text |

**Features:**
- Daily P&L summary bar at top (total day P&L, # of trades, win/loss count)
- Add trade form (slide-out panel or modal)
- Edit / delete existing trades
- Color-coded P&L rows
- Mini TradingView chart inline per trade row (auto-loads from ticker)
- Large TradingView Advanced Chart above the table (ticker switchable)

**No R-multiple / stop price field** — skipped per user preference.

---

### 2. Best Opps of the Day

**Purpose:** Track top movers and market opportunities — stocks that made notable moves each day, traded or not.

**Workflow:**
1. Pre-market: Pull from saved Watchlist, select today's focus stocks
2. Post-market: Fill in % move, direction, pattern, notes for each

**Fields per Opp entry:**
| Field | Type |
|---|---|
| Date | Auto (today) |
| Ticker | From watchlist or manual |
| % move | Number (e.g. +8.4%) |
| Direction | Long / Short opportunity |
| Pattern | Dropdown (from Playbook) |
| Status | Watched / Traded / Missed |
| Notes | Freeform text |

**Watchlist management:**
- Persistent saved list of tickers (stored in `Watchlist` sheet tab)
- Add/remove tickers from the saved list
- Each day, select from saved list to populate today's opps
- Can also add one-off tickers not on the saved list

**Charts:** Mini TradingView chart per opp row + large chart switchable at top.

---

### 3. Performance / P&L

**Purpose:** Analytics across all logged trades.

**Metrics:**
- Daily P&L for the selected date (bar or card)
- Weekly P&L running total
- Monthly P&L running total
- Overall win rate (%)
- Best trade ever ($ amount + ticker)
- Worst trade ever ($ amount + ticker)
- Equity curve — cumulative P&L line chart over time
- Setup / pattern breakdown table: # of trades, win rate, total P&L per pattern

**No R-multiple or avg-R tracking** — skipped per user preference.

---

### 4. Patterns / Playbook

**Purpose:** Personal catalog of trading setups.

**Pre-loaded patterns (from PineScript Value Area strategy):**

| Pattern | Entry Condition |
|---|---|
| Value Area Long (VAL) | Price below VAL + Stochastic RSI < threshold |
| POC Reclaim | Price reclaims the Point of Control from below |
| VAH Breakout | Price breaks above Value Area High |
| LOWBB | Price drops below Lower Bollinger Band by threshold % |
| HIGHBB | Price spikes above Upper Bollinger Band by threshold % |

**Fields per pattern:**
| Field | Type |
|---|---|
| Name | Text |
| Category | Dropdown (Momentum / Mean Reversion / Breakout / Value Area) |
| Description | Rich text |
| Entry criteria | Text |
| Exit criteria | Text |
| Notes / observations | Text |
| Chart image | Google Drive link (upload via Drive, paste link) |
| TradingView embed | Optional symbol/ticker for reference chart |

**Auto-linked stats (pulled from Trades sheet):**
- Times this pattern was tagged in a trade
- Win rate when pattern is used
- Avg P&L per trade when pattern is used

---

### 5. Pre-Market Bias & Notes

**Purpose:** Morning planning section before the open.

**Fields:**
| Field | Type |
|---|---|
| Date | Auto (today) |
| Market bias | Bullish / Bearish / Neutral / No trade day |
| Key levels | Table — label + price (e.g. "SPY support: 520.00") |
| Catalysts / news | Freeform text |
| Today's watchlist | Selected from saved Watchlist (links to Best Opps) |
| Additional notes | Freeform text |

One entry per trading day. Displayed as a card with edit capability.

---

### 6. Weekly / Monthly Review

**Purpose:** Auto-aggregated summaries by week and month.

**Auto-populated (from Trades sheet, no manual entry):**
- P&L total for the selected week / month
- Win rate for the period
- # of trades in the period
- Best trade of the period
- Worst trade of the period
- Pattern performance breakdown for the period
- Day-by-day P&L mini bar chart

**Manual section:**
- Observations / key lessons text area (stored in Sheets, keyed by week/month)

---

## TradingView Chart Integration

- **Advanced Chart widget** (large, interactive): Shown at top of Trade Log and Best Opps tabs. User can type a ticker to switch it.
- **Mini Symbol Overview widget** (small, inline): Embedded per trade row and per opp row, auto-loads from the row's ticker field.
- Both use TradingView's free public embed (no API key required).
- Theme: dark (matches dashboard theme).

---

## Google Sheets Integration

- Connect via **Google Sheets API v4** (Service Account JSON key for no-auth single-machine use)
- Reads on page/tab load; writes on form submit
- Optimistic UI: update local state immediately, sync to Sheets in background
- Error handling: show a small sync status indicator (green check / red warning)

---

## Build Order

Steps are sequential. Build does not start until user reviews this plan.

| # | Step | Notes |
|---|---|---|
| 1 | Project scaffold | Vite + React + Tailwind, folder structure |
| 2 | Google Sheets API layer | Service account setup, read/write helpers |
| 3 | Navigation shell + dark theme | Left sidebar, routing, electric blue accent |
| 4 | Home / Dashboard screen | Summary cards, quick-add buttons |
| 5 | Daily Trade Log tab | Form, table, P&L calc, color coding |
| 6 | Performance / P&L tab | Metrics, equity curve chart |
| 7 | Best Opps tab + Watchlist | Watchlist management, opp logging |
| 8 | Pre-Market Bias tab | Daily planning form |
| 9 | Patterns / Playbook tab | Pattern catalog, Drive image links, auto-stats |
| 10 | Weekly / Monthly Review tab | Auto-aggregation, period selector |
| 11 | TradingView chart embeds | Large + mini charts across tabs |
| 12 | Polish & review | Spacing, responsiveness, edge cases |

---

## Resolved Decisions

| Question | Decision |
|---|---|
| Instruments | Stocks / Equities only |
| Tech stack | React + Tailwind (Vite) |
| Data entry | Manual form |
| Data storage | Google Sheets (master sheet, tabs per section) |
| Auth | None — single machine |
| Navigation | Left sidebar |
| Landing page | Home / dashboard summary screen |
| Accent color | Electric blue |
| R-multiple tracking | Skipped — no stop price field |
| Pattern images | Google Drive links |
| TradingView charts | Large + mini, both on Trade Log and Best Opps |
| Best Opps style | Watchlist-based, 6–10 stocks |
| Playbook pre-loads | 5 Value Area / BB patterns from PineScript strategy |
| Extra tabs | Weekly/Monthly Review + Pre-Market Bias |
