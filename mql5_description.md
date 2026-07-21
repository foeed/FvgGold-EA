# FvgGold — Fair Value Gap EA for XAUUSD

## Overview

FvgGold is a free, open-source Expert Advisor for trading Gold (XAUUSD) on MetaTrader 5. It uses institutional trading concepts — Fair Value Gaps (FVG), Order Blocks, and killzone sessions — to enter high-probability trades with a fixed risk-to-reward ratio.

Unlike most free EAs that use dangerous grid or martingale systems, FvgGold takes one quality trade at time, scored and filtered by multiple confluence factors.

## Key Features

### Quality Scoring System (0–100)
Every Fair Value Gap is scored across five weighted components. Only setups meeting the minimum score threshold are traded. This filters out weak patterns and keeps only the best opportunities.

### Order Block Confluence
When an FVG overlaps with an institutional Order Block, the setup receives a +20 score bonus. This ensures entries are backed by institutional supply/demand zones.

### H1 EMA Trend Filter
The EA uses the H1 50/200 EMA crossover to determine the higher timeframe bias. Trades are only taken in the direction of the trend — buys when fast EMA is above slow EMA, sells when below.

### London/NY Killzone Timing
The EA only activates during peak liquidity windows:
- London Open: 07:00–10:00 GMT
- London/NY Overlap: 12:00–16:00 GMT
- NY Session: up to 21:00 GMT

### Fixed Risk-to-Reward
Each trade uses a fixed 1.5 R:R ratio. Entry is placed near the FVG edge (not midpoint) with stop-loss beyond the zone and take-profit at 1.5× the risk distance.

### Risk Management
- Fixed lot sizing (default 0.01)
- Maximum 1 concurrent trade
- Daily loss limit ($5 default)
- Break-even at 0.5× ATR profit
- On-chart dashboard with real-time session info

## Backtest Results

All results are from the MT5 Strategy Tester on XAUUSD.m, M15 timeframe, 0.01 lot, starting balance $10,000.

3-Month (April–July 2026):
- Start: $10,000 → Final: $10,522
- Return: +5.2%
- Win Rate: 40.0%
- Total Trades: 35

6-Month (January–July 2026):
- Start: $10,000 → Final: $14,874
- Return: +48.7%
- Win Rate: 45.3%
- Total Trades: 64

Optimized parameters: MinScoreFVG=50, RiskRewardRatio=1.5, FVGBuffer=3.0

## How to Install

1. Download FvgGold.mq5 from the product files
2. Copy it to: C:\Users\YourName\AppData\Roaming\MetaQuotes\Terminal\TerminalID\MQL5\Experts\
3. Open MetaTrader 5, press F4 to open MetaEditor
4. Open FvgGold.mq5 and press Compile (F7)
5. Open a XAUUSD chart on M15 timeframe
6. Drag FvgGold from the Navigator onto the chart
7. Enable "Allow Algo Trading" and click OK

## Input Parameters

### FVG Detection
FVGTimeframe (ENUM_TIMEFRAMES) — Execution and FVG detection timeframe. Default: M15.
MinGapSize (int) — Minimum FVG size in points. Default: 25.
MaxAgeBars (int) — Maximum FVG age in bars. Default: 20.
MinAgeBars (int) — Minimum FVG age in bars. Default: 2.
FVGBuffer (double) — Buffer beyond FVG edge for SL in price units ($). Default: 3.0.

### FVG Quality Scoring
MinScoreFVG (double) — Minimum FVG quality score to take a trade (0–100). Default: 50.
ScoreGapWeight (double) — Score weight for gap size component (0–30). Default: 30.
ScoreDispWeight (double) — Score weight for displacement/candle strength (0–30). Default: 30.
ScoreHTFWeight (double) — Score weight for H1 EMA alignment (0–20). Default: 20.
ScoreFreshWeight (double) — Score weight for zone freshness (0–10). Default: 10.
ScorePDWeight (double) — Score weight for premium/discount zone (0–10). Default: 10.

### Order Block Confluence
UseOBFilter (bool) — Require Order Block confluence for entries. Default: true.
OB_ImpulseATR (double) — Minimum impulse candle body = ATR × this value. Default: 1.5.
OB_LookbackBars (int) — How far back to scan for Order Blocks. Default: 50.
OB_MinSize (double) — Minimum Order Block size in points. Default: 10.
OB_ConfluenceBonus (double) — Score bonus when FVG overlaps an Order Block. Default: 20.
OB_MaxDistance (double) — Maximum distance between FVG and OB to count (points). Default: 200.

### Higher Timeframe Bias
HTF_Period (ENUM_TIMEFRAMES) — Higher timeframe for trend filter. Default: H1.
EMA_Fast (int) — Fast EMA period. Default: 50.
EMA_Slow (int) — Slow EMA period. Default: 200.

### Risk Management
FixedLot (double) — Fixed lot size. Set to 0 for risk-based sizing. Default: 0.01.
RiskPercent (double) — Risk per trade as percentage (used when FixedLot=0). Default: 0.5.
MaxTrades (int) — Maximum concurrent trades. Default: 1.
DailyLossLimit (double) — Daily loss limit in USD (0 = off). Default: 5.0.
ATR_TP_Mult (double) — ATR multiplier for TP (used when UseFixedRR=false). Default: 2.0.
RiskRewardRatio (double) — Fixed risk-to-reward ratio for take-profit. Default: 1.5.
UseFixedRR (bool) — Use fixed R:R instead of ATR-based TP. Default: true.

### Killzone / Session Filter
UseKillzone (bool) — Enable session time filter. Default: true.
KZ_LondonStart (int) — London open start hour (GMT). Default: 7.
KZ_LondonEnd (int) — London open end hour (GMT). Default: 10.
KZ_OverlapStart (int) — London/NY overlap start hour (GMT). Default: 12.
KZ_OverlapEnd (int) — London/NY overlap end hour (GMT). Default: 16.
KZ_NYEnd (int) — NY session end hour (GMT). Default: 21.
KZ_PreferOverlap (bool) — Only trade during overlap window (highest quality). Default: true.
CloseAtEOD (bool) — Close all positions at session end. Default: false.

### Miscellaneous
MagicNumber (ulong) — Unique magic number for this EA instance. Default: 7777.
SlippagePoints (int) — Maximum allowed slippage in points. Default: 20.

## Strategy Explained

FvgGold uses a five-step process for each trade:

1. Detect — Scans M15 for three-candle Fair Value Gap patterns (institutional imbalance zones)
2. Score — Rates each FVG 0–100 based on gap size, candle displacement, H1 trend alignment, freshness, and premium/discount positioning
3. Confirm — Checks for Order Block confluence (+20 bonus) and H1 EMA 50/200 trend direction
4. Filter — Only activates during London/NY killzone sessions for peak liquidity
5. Execute — Places a limit order near the FVG edge with fixed 1.5 R:R and manages via break-even

## Requirements

- MetaTrader 5 (build 3000 or higher)
- Broker with XAUUSD symbol
- Minimum balance: $10,000 (for 0.01 lot)
- Windows operating system

## What's New in v2.0

- FVG quality scoring system (0–100)
- Order Block confluence detection
- H1 EMA 50/200 trend filter
- London/NY killzone session filter
- Fixed R:R take-profit (1.5)
- Break-even management
- Daily loss limit protection
- On-chart dashboard with session info
- Full MQL5 source code (MIT License)

## Support

- Source code: github.com/foeed/FvgGold-EA
- Issues and feature requests: GitHub Issues
- Free to use, modify, and distribute under MIT License
