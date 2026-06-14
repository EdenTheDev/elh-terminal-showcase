# ELH Terminal

A multi-asset trading terminal I've been building. It's live at **[elhterminal.com](https://elhterminal.com)**.

Most retail traders end up with five tabs open: TradingView for charts, a couple of sites for derivatives and liquidations, something for wallets, and a separate app for journalling. ELH Terminal is my attempt to put that lot behind one login — live markets, derivatives data, backtesting, an on-chain wallet tracker, a perps journal and an AI research desk.

It started as my final-year computer science dissertation at Nottingham Trent University (submitted 2026), and I've kept building it into a real product since.

![Backend](https://img.shields.io/badge/Backend-FastAPI%20%2F%20Python%203.11-009688?style=flat-square)
![Frontend](https://img.shields.io/badge/Frontend-React%2019%20%2F%20TypeScript-3178C6?style=flat-square)
![Database](https://img.shields.io/badge/Database-PostgreSQL%20(Neon)-336791?style=flat-square)
![AI](https://img.shields.io/badge/AI-Gemini%202.5%20Flash-4285F4?style=flat-square)
![Live](https://img.shields.io/badge/Live-elhterminal.com-22c55e?style=flat-square)

> The source repo is private. Happy to give recruiters a walkthrough or a live demo on request.

## What it does

**Markets.** Live crypto over Binance websockets and equities/indices over Finnhub, drawn on Lightweight Charts. On top of that sits the derivatives layer most retail tools paywall: funding and open interest, a Coinglass-style liquidation heatmap, a Bookmap-style depth heatmap, on-chart order-flow footprint with stacked imbalances and sweeps, a gamma-exposure panel, and a derivatives screener.

**Strategy Lab.** A block-based backtester. Around 28 strategies, from plain MA/EMA crossovers up to ICT setups (fair-value gaps, order blocks, market-structure shifts, silver bullet). Each strategy is just an entry trigger; exits, bias filters and session filters are separate layers you compose on top, so you can mix and match without rewriting anything. Walk-forward validation, MAE/MFE, the usual risk metrics, and a Pine-to-Python transpiler that runs a TradingView script in a sandbox.

**Perps journal.** For leveraged trading. Log trades by hand, import a CSV, or pull them straight from Hyperliquid. It enriches each one with MAE/MFE against the klines and gives you roughly 20 stats — expectancy, profit factor, R-multiples, Kelly, risk of ruin, streaks — plus an hour-of-day heatmap and a one-line "biggest leak / strongest edge" read.

**Wallet tracker.** A Hyperliquid leaderboard with smart-money flow, a follow list, and alerts when a wallet you follow changes position (Discord, Telegram or email).

**Research desk.** Two market briefs a weekday, generated off a TradingView node, rendered to PDF, and graded against price after the fact so the track record is auditable rather than just claimed.

**Connections.** Read-only API links to Binance, Hyperliquid and Trading 212 that fill in your portfolio and journal automatically, so you're not retyping trades.

**Macro.** A Fed-data regime model (FRED) and a crypto composite score for the top-down read.

**AI assistant.** A Gemini chat that knows which screen you're on and can see your portfolio and journal (with balances masked).

There's also a composable multi-pane "Desk" workspace, a beginner/pro mode toggle, a PWA build, and it all works on mobile.

## Stack

| | |
|---|---|
| Backend | Python 3.11, FastAPI, PostgreSQL (Neon), pandas / NumPy |
| Frontend | React 19, TypeScript (strict), Vite, Tailwind, Lightweight Charts, Recharts |
| Auth | JWT in an HttpOnly cookie, CSRF double-submit, TOTP 2FA, email verification |
| AI | Gemini 2.5 Flash, server-side only |
| Data | Binance, Finnhub, Hyperliquid, FRED, CoinGecko, Yahoo Finance |
| Hosting | Render (backend, Frankfurt), Neon (database), Vercel (frontend) |

Around 560 backend tests, TypeScript strict throughout, and every third-party key stays server-side — the browser only ever sees shaped responses.

---

Built by Eden Hendry. Live at [elhterminal.com](https://elhterminal.com).
