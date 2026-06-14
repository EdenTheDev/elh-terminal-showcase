# ELH Terminal

A multi-asset trading terminal I've been building. It's live at **[elhterminal.com](https://elhterminal.com)**.

Most retail traders end up with five tabs open: TradingView for charts, a couple of sites for derivatives and liquidations, something for wallets, and a separate app for journalling. ELH Terminal is my attempt to put that lot behind one login — live markets, a full derivatives layer, strategy backtesting, an on-chain wallet tracker, a perps journal and an AI research desk, all over one per-user data model.

It started as my final-year computer science dissertation at Nottingham Trent University (submitted 2026), and I've kept building it into a real product since.

![Backend](https://img.shields.io/badge/Backend-FastAPI%20%2F%20Python%203.11-009688?style=flat-square)
![Frontend](https://img.shields.io/badge/Frontend-React%2019%20%2F%20TypeScript-3178C6?style=flat-square)
![Database](https://img.shields.io/badge/Database-PostgreSQL%20(Neon)-336791?style=flat-square)
![AI](https://img.shields.io/badge/AI-Gemini%202.5%20Flash-4285F4?style=flat-square)
![Tests](https://img.shields.io/badge/Tests-~560%20backend-success?style=flat-square)
![Live](https://img.shields.io/badge/Live-elhterminal.com-22c55e?style=flat-square)

> The source repo is private. Happy to give recruiters a full walkthrough or a live demo on request.

## What it does

**Markets and derivatives.** Live crypto over Binance websockets and equities/indices over Finnhub, drawn on Lightweight Charts. On top of price sits the derivatives layer most retail tools paywall: funding and open interest, a Coinglass-style liquidation heatmap, a Bookmap-style order-book depth heatmap, on-chart order-flow footprint with stacked imbalances and sweep detection, a gamma-exposure (GEX) panel, anchored VWAPs and liquidation-magnet levels.

**Strategy Lab.** A block-based backtester (more on the architecture below). Around 28 strategies across trend, momentum, mean-reversion, breakout, adaptive and ICT families, on any Binance pair or yfinance ticker from 15-minute up to weekly. Walk-forward validation with a configurable fold count and out-of-sample ratio, MAE/MFE, R-multiples, and a full risk metric set. You can also write your own strategy in Python — it runs in the browser under Pyodide and is simulated server-side through the same engine — and a Pine-to-Python transpiler ports a TradingView script into that same sandbox.

**Perpetuals journal.** Log trades by hand, import a CSV, or pull them straight from Hyperliquid. Each one is enriched with MAE/MFE against the klines, and you get ~20 statistics (expectancy, profit factor, R-multiples, Sharpe, Kelly, risk of ruin, streaks, size consistency), an hour-of-day heatmap, an excursion scatter, partial closes, and a one-line "biggest leak / strongest edge" read.

**Wallet tracker.** A Hyperliquid leaderboard built off a websocket harvester, with smart-money flow, a follow list, and alerts when a wallet you follow changes position — in-app, Discord, Telegram or email.

**Research desk.** Two market briefs a weekday, generated off a TradingView node, signed into the backend, rendered to PDF, and then graded against price after the fact — so the directional track record is auditable rather than just claimed (hit rate, expectancy, cumulative R, broken down by conviction tier).

**Connections.** Read-only, encrypted API links to Binance, Hyperliquid and Trading 212 that auto-populate the portfolio and journal, so trades aren't entered by hand.

**Macro.** A four-regime Fed-data model (FRED) and a 0–100 crypto composite score across liquidity, leverage, on-chain, sentiment and momentum.

**AI assistant.** A Gemini chat grounded in the current screen and the user's portfolio and journal (balances masked), proxied entirely server-side.

Plus a composable multi-pane "Desk" workspace, a beginner/pro mode toggle, and a PWA build that works on mobile.

## How it's built

| Layer | Stack |
|---|---|
| Backend | Python 3.11, FastAPI, Uvicorn, Pydantic v2, PostgreSQL (Neon), pandas, NumPy |
| Frontend | React 19, TypeScript (strict), Vite, Tailwind, Lightweight Charts, Recharts |
| Real-time | Binance WS (direct), Finnhub WS (proxied), Hyperliquid WS (wallet harvester) |
| AI | Gemini 2.5 Flash, server-side with daily caps and a response cache |
| Hosting | Render (backend, Frankfurt), Neon (database, EU), Vercel (frontend) |

A few things hold across the codebase:

- **Keys stay server-side.** Every third-party API — Binance, Finnhub, FRED, Gemini, Hyperliquid — is called from the backend. The browser only ever receives shaped responses and never holds a secret.
- **Metrics are deterministic.** Scores, backtests and journal statistics are pure functions over stored inputs and reproduce exactly from the database — the same config and the same klines always give the same numbers.
- **Dev mirrors prod.** Local runs against the same Postgres dialect and the same Pydantic contracts that serve production.

### The backtester

The quant engine is a `BaseBlock` pipeline — each block adds columns to the OHLCV frame in turn:

- **Strategy blocks** are pure entry triggers; they only emit the signal.
- **A bias-filter block** is an optional regime overlay (higher-timeframe trend, market structure, liquidity) that masks signals which disagree with context.
- **An exit-policy block** is a strategy-agnostic layer for take-profit, stop, trailing stop, break-even and time-stop in percent / ATR / structural modes.

Keeping entries, bias and exits as separate composable layers means any strategy can be A/B tested against any exit or regime filter without rewriting it. Exits resolve against each bar's high/low (intrabar, not the close) and pessimise to the stop on a same-bar collision, with trailing and break-even computed off prior-bar extremes to avoid within-bar look-ahead — the difference between a backtest that flatters itself and one you can trust.

### Security

JWT in an HttpOnly cookie (unreadable from JS), CSRF double-submit on every mutating request, HMAC-SHA256 on the machine-to-machine report ingest, TOTP 2FA and email verification, `slowapi` rate limits on auth and compute-heavy routes, Pydantic validation at every boundary (regex, `Literal` enums, bounded ranges), and per-user isolation enforced in every query. Around 560 backend tests run DB-free against mocked dependencies, with strict TypeScript across the frontend and CI on every PR.

## Quant roadmap

The direction is to turn the discretionary research into a measured, robustness-tested engine:

- **Stochastic price simulation** — GBM and GARCH(1,1) path generation (with Student-t shocks for crypto's fat tails), Monte-Carlo VaR/CVaR via Cholesky-decomposed return covariance, and an Ornstein-Uhlenbeck process for spreads and funding rates.
- **Strategy robustness battery** — run a strategy across many simulated and bootstrapped paths rather than one historical path, and look at the *distribution* of Sharpe, drawdown and return; permutation tests against shuffled entries; parameter-perturbation stability.
- **A signals engine** — a deterministic, parameterised research model searched for robust out-of-sample survivors, gated on walk-forward + held-out + Monte-Carlo criteria fixed before the run, then forward-paper-tested before it ever fires.
- **A paper-trading engine and leaderboard** — forward-only virtual accounts tracking live signals, with a verified, risk-adjusted track record.

---

Built by Eden Hendry. Live at [elhterminal.com](https://elhterminal.com).
