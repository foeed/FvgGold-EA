# FvgGold — Fair Value Gap EA for XAUUSD

A quality-scored Fair Value Gap (FVG) order-entry EA for MetaTrader 5, with Order Block confluence, H1 EMA trend filter, and killzone timing.

## Strategy

FvgGold identifies high-probability FVG setups using a multi-factor quality score (0–100):

- **Gap Size** (0–30): FVG width relative to ATR
- **Displacement** (0–30): Body/range ratio of the impulse candle
- **HTF Alignment** (0–20): FVG direction matches H1 EMA 50/200 trend
- **Freshness** (0–10): How recently the FVG formed
- **Premium/Discount** (0–10): Price vs H1 EMA — buys in discount, sells in premium
- **OB Confluence** (+20): Bonus if FVG overlaps a bullish/bearish Order Block

Orders are placed at FVG edges (not midpoint) with fixed R:R take-profit.

## Backtest Results

| Period | Start Bal | Final Bal | Return | Win Rate | Trades |
|--------|-----------|-----------|--------|----------|--------|
| 3-month (Apr–Jul 2026) | $100 | $105.22 | +5.2% | 40.0% | 35 |
| **6-month (Jan–Jul 2026)** | **$100** | **$148.74** | **+48.7%** | **45.3%** | **64** |

**Optimized parameters:** M15, MinScoreFVG=50, R:R=1.5, FVGBuffer=3.0, 0.01 lot

## Installation

### 1. Copy EA to MT5

Copy `FvgGold.mq5` to your MetaTrader 5 data folder:

```
C:\Users\<YourUser>\AppData\Roaming\MetaQuotes\Terminal\<TerminalID>\MQL5\Experts\
```

### 2. Compile

- Open MetaTrader 5
- Press **F4** to open MetaEditor
- Open `FvgGold.mq5` from the Navigator panel
- Press **Compile** (or F7)
- Verify: 0 errors, 0 warnings

### 3. Attach to Chart

- Open a **XAUUSD** chart on **M15** timeframe
- Drag `FvgGold` from the Navigator onto the chart
- Enable "Allow Algo Trading"
- Click OK (default settings are pre-optimized)

### 4. Load Optimized Settings

To load the pre-optimized parameters:

1. In the EA properties dialog, go to the **Inputs** tab
2. Click **Load** and select `FvgGold.set`
3. Click OK

## Parameters

### FVG Detection
| Parameter | Default | Description |
|-----------|---------|-------------|
| FVGTimeframe | M15 | Execution and FVG detection timeframe |
| MinGapSize | 25 | Minimum FVG size in points |
| MaxAgeBars | 20 | Maximum FVG age in bars |
| MinAgeBars | 2 | Minimum FVG age in bars |
| FVGBuffer | 3.0 | Buffer beyond FVG edge for SL (price units) |

### Quality Scoring
| Parameter | Default | Description |
|-----------|---------|-------------|
| MinScoreFVG | 50 | Minimum FVG quality score to trade |
| ScoreGapWeight | 30 | Weight for gap size component |
| ScoreDispWeight | 30 | Weight for displacement component |
| ScoreHTFWeight | 20 | Weight for HTF alignment component |
| ScoreFreshWeight | 10 | Weight for freshness component |
| ScorePDWeight | 10 | Weight for premium/discount component |

### Order Block
| Parameter | Default | Description |
|-----------|---------|-------------|
| UseOBFilter | true | Require OB confluence |
| OB_ImpulseATR | 1.5 | Min impulse body = ATR × this |
| OB_LookbackBars | 50 | OB scan lookback |
| OB_MinSize | 10 | Min OB size in points |
| OB_ConfluenceBonus | 20 | Score bonus for FVG+OB overlap |

### Risk Management
| Parameter | Default | Description |
|-----------|---------|-------------|
| FixedLot | 0.01 | Fixed lot size |
| MaxTrades | 1 | Max concurrent trades |
| DailyLossLimit | 5.0 | Daily loss limit in USD |
| RiskRewardRatio | 1.5 | Fixed R:R take-profit |
| UseFixedRR | true | Use fixed R:R instead of ATR-based TP |

### Killzone
| Parameter | Default | Description |
|-----------|---------|-------------|
| UseKillzone | true | Enable session filter |
| KZ_LondonStart | 7 | London open (GMT) |
| KZ_LondonEnd | 10 | London open end (GMT) |
| KZ_OverlapStart | 12 | London/NY overlap start (GMT) |
| KZ_OverlapEnd | 16 | London/NY overlap end (GMT) |
| KZ_NYEnd | 21 | NY session end (GMT) |

## Files

```
FvgGold-EA/
├── FvgGold.mq5       # EA source code
├── FvgGold.set       # Optimized parameter set
├── LICENSE            # MIT License
└── README.md          # This file
```

## Requirements

- MetaTrader 5 (build 3000+)
- Broker with XAUUSD symbol
- Minimum balance: $100 (for 0.01 lot)

## License

MIT License — see [LICENSE](LICENSE) for details.
