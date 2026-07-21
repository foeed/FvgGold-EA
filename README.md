# FvgGold — XAUUSD Fair Value Gap (FVG) Trading Bot for MetaTrader 5

**Algorithmic gold trading EA** that identifies high-probability Fair Value Gap setups with Order Block confluence, H1 EMA trend filter, and London/NY killzone timing. Built for XAUUSD on MetaTrader 5.

[![Website](https://img.shields.io/badge/Website-foeed.github.io/FvgGold-EA-blue.svg)](https://foeed.github.io/FvgGold-EA/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Platform](https://img.shields.io/badge/Platform-MetaTrader%205-blue.svg)](https://www.metatrader5.com/)
[![Symbol](https://img.shields.io/badge/Symbol-XAUUSD-gold.svg)]()
[![Win Rate](https://img.shields.io/badge/Win%20Rate-45.3%25-green.svg)]()
[![Return](https://img.shields.io/badge/6%20Month%20Return-%2B48.7%25-brightgreen.svg)]()

---

> **[View Landing Page](https://foeed.github.io/FvgGold-EA/)** — modern UI with interactive demo

---

## Table of Contents

- [What is FvgGold?](#what-is-fvggold)
- [How It Works](#how-it-works)
- [Backtest Results](#backtest-results)
- [Installation](#installation)
- [Parameters](#parameters)
- [FAQ](#faq)
- [Requirements](#requirements)
- [License](#license)

---

## What is FvgGold?

FvgGold is an **open-source MQL5 Expert Advisor** for automated gold (XAUUSD) trading on MetaTrader 5. It uses institutional trading concepts — **Fair Value Gaps (FVG)**, **Order Blocks (OB)**, and **killzone sessions** — to enter high-probability trades with a fixed risk-to-reward ratio.

The EA scores each FVG setup on a 0–100 quality scale across five factors, then only takes trades that meet a minimum score threshold. This filters out weak setups and keeps only the best opportunities.

**Key features:**
- Quality-scored FVG detection (gap size, displacement, HTF alignment, freshness, premium/discount)
- Order Block confluence (+20 score bonus)
- H1 EMA 50/200 trend filter
- London / NY overlap killzone session filter
- Fixed R:R take-profit (1.5)
- Break-even management
- Daily loss limit protection
- On-chart dashboard with real-time session and bias info

---

## How It Works

### 1. FVG Detection & Quality Scoring

Each FVG is scored on a **0–100 scale** across five components:

| Component | Weight | What It Measures |
|-----------|--------|------------------|
| **Gap Size** | 0–30 | FVG width relative to ATR — larger gaps score higher |
| **Displacement** | 0–30 | Body/range ratio of the impulse candle — strong momentum scores higher |
| **HTF Alignment** | 0–20 | FVG direction matches H1 EMA 50/200 trend |
| **Freshness** | 0–10 | How recently the FVG formed — newer zones score higher |
| **Premium/Discount** | 0–10 | Buys in discount zone (below H1 EMA), sells in premium (above H1 EMA) |
| **OB Confluence** | +20 bonus | FVG overlaps a bullish or bearish Order Block |

Only setups scoring **above MinScoreFVG (default: 50)** are considered for entry.

### 2. Entry Logic

- **Buy limit** placed at FVG bottom edge + buffer (enters near the zone, not the midpoint)
- **Sell limit** placed at FVG top edge - buffer
- Fixed **R:R = 1.5** take-profit from entry
- Stop-loss placed at FVG edge minus buffer (price units)

### 3. Session Filter

The EA only trades during **London/NY killzones** (configurable in GMT):

- **London Open:** 07:00–10:00
- **London/NY Overlap:** 12:00–16:00
- **NY Close:** 21:00

### 4. Risk Management

- Fixed lot sizing (default 0.01)
- Max 1 concurrent trade
- Daily loss limit ($5 default)
- Break-even at 0.5× ATR profit

---

## Backtest Results

All results on **XAUUSD.m, M15 timeframe**, 0.01 lot, starting balance $10,000.

| Period | Start Balance | Final Balance | Return | Win Rate | Trades |
|--------|---------------|---------------|--------|----------|--------|
| 3-month (Apr–Jul 2026) | $10,000 | $10,522.00 | +5.2% | 40.0% | 35 |
| **6-month (Jan–Jul 2026)** | **$10,000** | **$14,874.00** | **+48.7%** | **45.3%** | **64** |

**Optimized parameters:** `MinScoreFVG=50`, `RiskRewardRatio=1.5`, `FVGBuffer=3.0`, `FixedLot=0.01`

> **Note:** These are historical backtest results. Past performance does not guarantee future results. Always test on a demo account before live trading.

---

## Installation

### Step 1: Copy the EA file

Copy `FvgGold.mq5` to your MetaTrader 5 data folder:

```
C:\Users\<YourUser>\AppData\Roaming\MetaQuotes\Terminal\<TerminalID>\MQL5\Experts\
```

### Step 2: Compile

1. Open **MetaTrader 5**
2. Press **F4** to open MetaEditor
3. Open `FvgGold.mq5` from the Navigator panel
4. Press **Compile** (or F7)
5. Verify: **0 errors, 0 warnings**

### Step 3: Attach to Chart

1. Open a **XAUUSD** chart on the **M15** timeframe
2. Drag `FvgGold` from the Navigator onto the chart
3. Enable **"Allow Algo Trading"**
4. Click **OK** (default settings are pre-optimized)

### Step 4: Load Optimized Settings (Optional)

1. In the EA properties dialog, go to the **Inputs** tab
2. Click **Load** and select `FvgGold.set`
3. Click **OK**

---

## Parameters

### FVG Detection

| Parameter | Default | Description |
|-----------|---------|-------------|
| `FVGTimeframe` | M15 | Execution and FVG detection timeframe |
| `MinGapSize` | 25 | Minimum FVG size in points |
| `MaxAgeBars` | 20 | Maximum FVG age in bars |
| `MinAgeBars` | 2 | Minimum FVG age in bars |
| `FVGBuffer` | 3.0 | Buffer beyond FVG edge for SL (price units) |

### Quality Scoring

| Parameter | Default | Description |
|-----------|---------|-------------|
| `MinScoreFVG` | 50 | Minimum FVG quality score to trade (0–100) |
| `ScoreGapWeight` | 30 | Weight for gap size component |
| `ScoreDispWeight` | 30 | Weight for displacement component |
| `ScoreHTFWeight` | 20 | Weight for HTF alignment component |
| `ScoreFreshWeight` | 10 | Weight for freshness component |
| `ScorePDWeight` | 10 | Weight for premium/discount component |

### Order Block Confluence

| Parameter | Default | Description |
|-----------|---------|-------------|
| `UseOBFilter` | true | Require Order Block confluence |
| `OB_ImpulseATR` | 1.5 | Min impulse body = ATR × this value |
| `OB_LookbackBars` | 50 | How far back to scan for OBs |
| `OB_MinSize` | 10 | Minimum OB size in points |
| `OB_ConfluenceBonus` | 20 | Score bonus for FVG + OB overlap |

### Risk Management

| Parameter | Default | Description |
|-----------|---------|-------------|
| `FixedLot` | 0.01 | Fixed lot size (0 = risk-based sizing) |
| `MaxTrades` | 1 | Maximum concurrent trades |
| `DailyLossLimit` | 5.0 | Daily loss limit in USD (0 = off) |
| `RiskRewardRatio` | 1.5 | Fixed R:R take-profit multiplier |
| `UseFixedRR` | true | Use fixed R:R instead of ATR-based TP |

### Killzone / Session Filter

| Parameter | Default | Description |
|-----------|---------|-------------|
| `UseKillzone` | true | Enable session filter |
| `KZ_LondonStart` | 7 | London open start (GMT) |
| `KZ_LondonEnd` | 10 | London open end (GMT) |
| `KZ_OverlapStart` | 12 | London/NY overlap start (GMT) |
| `KZ_OverlapEnd` | 16 | London/NY overlap end (GMT) |
| `KZ_NYEnd` | 21 | NY session end (GMT) |
| `KZ_PreferOverlap` | true | Only trade during overlap window |

---

## FAQ

### What is a Fair Value Gap (FVG)?

A Fair Value Gap is a three-candle pattern where candle 1's high is below candle 3's low (bullish) or candle 1's low is above candle 3's high (bearish). The gap between them is the FVG — an institutional imbalance that price tends to revisit.

### What timeframe is best for XAUUSD?

FvgGold is optimized for **M15** (15-minute). Higher timeframes like M30 and H1 produce fewer but higher-quality signals. M5 produces too many signals and lower win rates.

### What is the minimum balance needed?

**$10,000** for 0.01 lot. The EA uses fixed lot sizing by default.

### Can I use this on other symbols?

Yes. While optimized for XAUUSD, you can use it on any symbol with sufficient liquidity and spread. Adjust `MinGapSize` and `FVGBuffer` for different instruments.

### What risk-to-reward ratio works best?

Backtesting shows **R:R = 1.5** is optimal. Higher ratios (2.0+) reduce win rate too much. Lower ratios (1.0) don't compensate for the 55% loss rate.

### Does this EA work on live accounts?

Yes, but **always test on a demo account first**. Past backtest results do not guarantee future performance.

### Is this EA free?

Yes. FvgGold is **open source** under the MIT License. You can use, modify, and distribute it freely.

---

## Files

```
FvgGold-EA/
├── FvgGold.mq5       # EA source code (MQL5)
├── FvgGold.set       # Optimized parameter set
├── docs/
│   └── index.html    # Landing page (GitHub Pages)
├── LICENSE            # MIT License
└── README.md          # This file
```

---

## Requirements

- **MetaTrader 5** (build 3000+)
- Broker with **XAUUSD** symbol
- Minimum balance: **$10,000** (for 0.01 lot)
- Windows OS (for MT5)

---

## Contributing

Contributions are welcome! Feel free to open issues or submit pull requests.

## License

MIT License — see [LICENSE](LICENSE) for details.

---

**Keywords:** XAUUSD EA, gold trading bot, Fair Value Gap, FVG trading, Order Block, MetaTrader 5 EA, algorithmic trading, forex bot, MQL5 expert advisor, gold scalping EA, MT5 trading system
