# ELH Terminal

**Live demo → [elhterminal.com](https://elhterminal.com)**

A full-stack quantitative trading terminal built as a BSc Computer Science dissertation at Nottingham Trent University. Bridges real-time crypto and equity markets with macroeconomic intelligence, strategy backtesting, and AI-assisted portfolio analysis.

---

## Tech Stack

| Layer | Technology |
|---|---|
| Backend | Python 3.11, FastAPI, PostgreSQL |
| Frontend | React 19, TypeScript, Vite, Tailwind CSS |
| Real-time | Binance WebSocket (direct), Finnhub WebSocket (proxied) |
| Quant engine | NumPy, Pandas, custom block-based pipeline |
| AI | Google Gemini 2.5 Flash |
| Auth | JWT (HttpOnly cookie) + CSRF double-submit pattern |
| Hosting | Render (backend), Vercel (frontend) |

---

## Features

### Live Market Data
- Real-time crypto prices via Binance WebSocket (BTC, ETH, SOL, BNB, XRP and more)
- Real-time equity and index quotes via Finnhub WebSocket
- Candlestick charts powered by TradingView Lightweight Charts v5

### Strategy Backtester
Simulates long-only strategies on historical OHLCV data with equity curve, trade log, Sharpe ratio, max drawdown, and win rate. Supports:

| Strategy | Description |
|---|---|
| MA20/50 Crossover | Simple moving average trend following |
| EMA9/21 Crossover | Fast EMA momentum |
| EMA20/50 Crossover | Medium-term EMA trend |
| Bollinger Bands | Mean reversion on band touches |
| Donchian Channel Breakout | N-period high/low breakout |
| RSI Mean Reversion | Oversold/overbought reversion |
| MACD Momentum | MACD signal line crossover |
| ATR Volatility Breakout | Volatility-adjusted breakout |

Crypto symbols fetch from Binance REST (native 4H/1D support). Non-crypto symbols fetch from Yahoo Finance with 1H→4H resampling where needed.

### Macroeconomic Dashboard
- Federal Reserve data via FRED API (interest rates, CPI, unemployment, M2, yield curve)
- Custom macro scoring algorithm combining indicators into a regime signal
- Crypto macro overlay (dominance, fear & greed, funding rates)

### Portfolio & Transaction Tracking
- Add, edit, and remove trades with cost-basis and PnL calculation
- Per-user data isolation with JWT-scoped queries
- Trade journal linked to transactions

### AI Analysis
- Conversational market analysis powered by Google Gemini 2.5 Flash
- Per-symbol AI report generation with grounding
- Rate-limited server-side proxy — API key never exposed to the browser

### Security
- JWT stored in HttpOnly cookie, never accessible to JavaScript
- CSRF double-submit cookie pattern on all mutating endpoints
- Rate limiting on compute-heavy and AI endpoints
- WebSocket concurrent connection cap per IP
- Input validation: symbol regex, interval/period whitelists, bounded date ranges

---

## Architecture

```
Browser
  │
  ├── Vercel (React SPA)
  │     └── JWT cookie + CSRF header on every authenticated request
  │
  └── Render (FastAPI)
        ├── /api/auth         — login, logout, session, password change
        ├── /api/market       — macro, scoring, news, forecast, backtest
        ├── /api/finnhub      — equity search, candles, WS proxy
        ├── /api/ai           — Gemini chat + analysis proxy
        └── /api/transactions — portfolio CRUD
              │
              ├── PostgreSQL   (users, transactions, trade journal)
              ├── Binance REST/WS   (crypto OHLCV + live prices)
              ├── Finnhub REST/WS   (equity OHLCV + live prices)
              ├── FRED REST         (macro indicators)
              └── Gemini API        (AI analysis)
```

---

## Screenshots

*Screenshots and demo video coming soon.*

---

## Project Context

Built as a final-year dissertation project exploring the intersection of quantitative finance, real-time systems, and applied AI. The system is designed as a prototype SaaS platform demonstrating institutional-style analytics for retail investors.

The private source repository is available on request for academic or recruitment purposes.
