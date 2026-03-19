# ELH Terminal

**Quantitative Portfolio Management System**

A full-stack financial analytics platform bridging real-time crypto and equity markets with macroeconomic intelligence, strategy backtesting, and AI-assisted portfolio analysis. Built as a final-year BSc dissertation project at Nottingham Trent University (2025–26).

![Backend](https://img.shields.io/badge/Backend-FastAPI%20%2F%20Python%203.11-009688?style=flat-square)
![Frontend](https://img.shields.io/badge/Frontend-React%2019%20%2F%20TypeScript-3178C6?style=flat-square)
![Database](https://img.shields.io/badge/Database-PostgreSQL-336791?style=flat-square)
![AI](https://img.shields.io/badge/AI-Gemini%202.5%20Flash-4285F4?style=flat-square)
![Live](https://img.shields.io/badge/Live-elhterminal.com-22c55e?style=flat-square)

---

## Live Demo

**[elhterminal.com](https://elhterminal.com)**

---

## Overview

ELH Terminal provides a unified interface for portfolio tracking, real-time market data, quantitative strategy backtesting, and macroeconomic regime analysis — all in one authenticated web application.

> Screenshots and demo video coming soon.

---

## Architecture

| Layer | Stack |
|---|---|
| Backend | Python 3.11, FastAPI, Uvicorn, PostgreSQL, Pandas, NumPy |
| Frontend | React 19, TypeScript, Vite, Tailwind CSS, Recharts, Lightweight Charts |
| Auth | JWT (HttpOnly cookie) + CSRF double-submit cookie |
| AI | Google Gemini 2.5 Flash (proxied server-side) |
| Hosting | Render (backend) + Vercel (frontend) |

### Data Sources

| Source | Data |
|---|---|
| Binance REST + WS | Live crypto prices, funding rate, open interest |
| Finnhub | Equity quotes, candles, WebSocket proxy |
| FRED (St. Louis Fed) | GDP, CPI, unemployment, interest rates, yield curve |
| CoinGecko | Market cap, BTC/ETH dominance, stablecoin supply |
| Alternative.me | Fear & Greed Index |
| Yahoo Finance | Indices (SPX, NDX), commodities, ETFs |

---

## Features

### Live Market Data
- Real-time crypto prices via Binance WebSocket (BTC, ETH, SOL, BNB, XRP and more)
- Real-time equity and index quotes via Finnhub WebSocket
- Candlestick charts powered by TradingView Lightweight Charts v5
- Quant metric strip: Beta, Volatility, Funding Rate, Stablecoin Peg, Drawdown

### Strategy Backtester
Simulates long-only strategies on historical OHLCV data. Returns equity curve, trade log, Sharpe ratio, max drawdown, and win rate. Crypto symbols use Binance REST; equities use Yahoo Finance.

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

### Macroeconomic Dashboard
- Federal Reserve data via FRED API with custom composite scoring
- Macro regime quadrant: Goldilocks / Stagflation / Overheating / Deflation
- Crypto macro overlay: composite signal 0–100 across Liquidity, Leverage, On-Chain, Sentiment, Institutional, and Quant categories
- Live radar chart and per-category breakdowns

### Portfolio & Transaction Tracking
- Transaction-based accounting with cost-basis and real-time PnL
- Performance metrics: Sharpe, Sortino, CAGR, VaR, Kelly Criterion, Max Drawdown
- Per-user data isolation enforced at the database layer

### AI Analysis
- Conversational market analysis via Google Gemini 2.5 Flash
- Per-symbol AI report generation with source grounding
- Server-side API proxy — key never exposed to the browser

### Security
- **JWT** stored in HttpOnly cookie — inaccessible from JavaScript
- **CSRF** double-submit cookie pattern on all mutating endpoints
- Rate limiting on compute-heavy and AI endpoints (slowapi)
- WebSocket concurrent connection cap per IP
- Input validation: symbol regex, interval/period whitelists, bounded date ranges

---

## Project Context

Built as a final-year dissertation project exploring the intersection of quantitative finance, real-time distributed systems, and applied AI. The system is designed as a prototype SaaS platform demonstrating institutional-style analytics accessible to retail investors.

The private source repository is available on request for academic or recruitment purposes.
