# Polymarket Liquidity X-Ray

Raw liquidity data from 68 days of continuous Polymarket monitoring.

**3,302,510 measurements. 353 markets. Jan 25 to Apr 3, 2026.**

A bot polled every active Polymarket market's order book every 5 minutes, 24/7, and recorded spreads, depth, and mid-prices.

## The finding

99.45% of all measurements showed a 100% spread (10,000 bps). Completely untradeable.

| Spread | Count | % of Total |
|--------|-------|-----------|
| 100% (illiquid) | 3,284,298 | 99.45% |
| 99.04% (near-illiquid) | 17,911 | 0.54% |
| 22.22% (liquid) | 131 | 0.004% |

Zero profitable trades were executed across the entire monitoring period.

## Data files

| File | Description | Rows |
|------|-------------|------|
| `liquidity_metrics.csv.gz` | **Full raw dataset** (gzipped CSV) | 3,302,510 |
| `daily_summary.json` | Per-day totals: markets tracked, measurements, liquid vs illiquid counts | 69 |
| `spread_distribution.json` | Aggregate spread bucket counts | 3 |
| `market_stats.json` | Per-market stats: measurement count, min spread, first/last seen, liquid periods | 353 |
| `hourly_patterns.json` | Measurements and liquid windows by hour of day (UTC) | 24 |
| `overview.json` | Top-level summary stats | 1 |
| `correlations.json` | Depth/spread correlation data | 1 |

## Schema

### liquidity_metrics.csv.gz (raw data)
```csv
market_id,timestamp,bid_depth,ask_depth,order_book_depth,mid_price,spread_bps
0x43ec785...,2026-01-25T23:50:06.260875+00:00,15694.4876,12935.1654,28629.653,0.8,10000
```
`market_id` is the Polymarket condition ID. `bid_depth`/`ask_depth` in USDC. `mid_price` is 0 to 1. `spread_bps` in basis points (10000 = 100%).

Decompress: `gunzip liquidity_metrics.csv.gz`

### daily_summary.json
```json
{
  "date": "2026-01-26",
  "markets_tracked": 215,
  "total_measurements": 39672,
  "illiquid_count": 39542,
  "liquid_count": 130
}
```

### market_stats.json
```json
{
  "market_id": "0x57cea...",
  "measurements": 17911,
  "min_spread_bps": 9904,
  "first_seen": "2026-01-31 09:09:38",
  "last_seen": "2026-04-03 17:21:15",
  "liquid_periods": 17911
}
```
`market_id` is the Polymarket condition ID (public contract address). `min_spread_bps` is the lowest spread observed in basis points. `liquid_periods` counts how many 5-min windows had spread < 10,000 bps.

### hourly_patterns.json
```json
{
  "hour_utc": 14,
  "measurements": 137604,
  "liquid_count": 754
}
```

### spread_distribution.json
```json
{
  "spread_bps": 10000,
  "count": 3284298
}
```

## Methodology

- Bot polled Polymarket's CLOB API every 5 minutes
- Recorded best bid, best ask, mid-price, bid depth, ask depth, and total order book depth
- Spread calculated as `(ask - bid) / mid_price * 10000` in basis points
- A market is "liquid" in a given window if spread < 10,000 bps
- All timestamps are UTC

## How to use this

**Research:** Liquidity analysis, market microstructure studies, prediction market efficiency
**Backtesting:** Test trading strategies against real spread data
**Building:** If you're building on Polymarket, this tells you which markets actually have order books

## License

CC BY 4.0. Use it, cite it, build on it.

Built by [@FathinDev](https://x.com/FathinDev)
