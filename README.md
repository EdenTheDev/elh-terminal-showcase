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

## Roadmap

Planned extensions to the quant engine and trade analytics layer, prioritised by analytical value.

### Advanced Trade Analytics Engine

| Feature | Description |
|---|---|
| **R-Multiple Framework** | Normalise all trade PnL into units of initial risk (R). Expectancy = mean R-multiple across all closed trades — a scale-invariant performance metric independent of position size |
| **MAE / MFE Tracking** | Record Maximum Adverse Excursion and Maximum Favorable Excursion per trade. Enables entry/exit efficiency scoring and stop placement optimisation |
| **Profit Factor & Expectancy Dashboard** | Extend the journal UI with 16+ derived tiles: Profit Factor (gross wins / gross losses), Expectancy (E[R]), Win Streak / Loss Streak, Average Hold Time by setup type |
| **Dynamic Kelly Sizing** | Compute the full Kelly fraction and fractional Kelly variants (½K, ¼K) from the live journaled win rate and W/L ratio — surfaced alongside portfolio risk metrics |
| **Consistency Score** | Standard deviation of risk per trade (σ of R-size). Low σ indicates disciplined position sizing; high σ flags discretionary drift |
| **Trade Screenshot Association** | Attach chart images to journal entries for pattern review and post-hoc analysis |

### Signal & Execution Research

| Feature | Description |
|---|---|
| **Macro Regime Filter** | Gate strategy entries by the composite macro signal (0–100). Backtester receives an optional `regime_filter` parameter; trades are only taken when the macro environment matches a target regime band |
| **Order Flow Module** | Volume delta, cumulative delta, and vector candles using Binance aggTrades endpoint. Tick-level imbalance as a leading signal layer on top of existing OHLCV strategies |
| **Paper Trading Engine** | Forward-only virtual account that executes live signals in real-time without capital risk. Tracks open positions, unrealised PnL, and daily NAV against a benchmark |
| **Alert System** | Configurable threshold alerts for price moves, macro score changes, and strategy signal triggers — delivered via browser notification or email |

### Stochastic Simulation & Probabilistic Analysis

Extends the backtester from historical evaluation into probabilistic forward analysis. Planned to live in a new `quant_engine/simulation/` module sitting alongside the existing `forecasting/` and `data_pipeline/` directories.

**Core simulation engine**

| Feature | Description |
|---|---|
| **GBM Price Path Simulator** | Fit drift (μ) and volatility (σ) from historical log returns, then generate N paths over a user-defined horizon using the Euler-Maruyama discretisation of Geometric Brownian Motion. Returns 5th/25th/50th/75th/95th percentile bands rendered as a fan chart |
| **Terminal Distribution** | Histogram of simulated prices at horizon T — exposes skewness and tail risk that a single point forecast conceals |
| **Monte Carlo VaR / CVaR** | Simulate correlated portfolio paths (Cholesky decomposition of the historical return covariance matrix). Sort terminal P&L distribution: 5th percentile = 95% VaR; mean below VaR = CVaR (Expected Shortfall). More accurate than parametric VaR for fat-tailed crypto returns and the preferred measure under Basel III |

**Strategy robustness testing**

| Feature | Description |
|---|---|
| **Simulated Path Backtesting** | Run any of the 8 existing strategies across N GBM-simulated paths rather than a single historical path. Returns the distribution of Sharpe ratio, max drawdown, win rate, and total return — directly addresses the question of whether strategy performance is genuine alpha or historical path-dependency |
| **Robustness Distribution UI** | Violin/box plot of metric distributions across paths, surfacing median, IQR, and outliers |

**Extended stochastic models**

| Model | Description |
|---|---|
| Student-t shocks | Replace standard normal noise with Student-t (ν ≈ 4 for crypto) to capture fat tails and excess kurtosis — a well-documented stylised fact in crypto return distributions |
| Ornstein-Uhlenbeck | Mean-reverting process (`dX = θ(μ−X)dt + σdW`) appropriate for spreads, funding rates, and macro indicators. Foundation for a pairs trading module |
| GARCH(1,1) | Time-varying volatility fitted from historical returns. Captures volatility clustering absent from constant-σ GBM |
| Heston Stochastic Volatility | Volatility follows its own CIR mean-reverting process. Produces an implied volatility smile — relevant for options pricing extensions |

---

## Project Context

Built as a final-year dissertation project exploring the intersection of quantitative finance, real-time distributed systems, and applied AI. The system is designed as a prototype SaaS platform demonstrating institutional-style analytics accessible to retail investors.

The private source repository is available on request for academic or recruitment purposes.
