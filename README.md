# Hyperliquid & Multi-Exchange Market Data — August 2026 Capture

Public market data collected from Hyperliquid and other perpetual futures
exchanges. No account, wallet, or private data is included — market data only.

## Bundles

| File | Contents |
|---|---|
| `hl-5m-candles.zip` | 5-minute OHLCV candles + hourly funding rates for ~2,900 Hyperliquid assets, covering ~2025-08-01 → 2026-08-22. Layout: `hl_candles/<COIN>/candles.parquet` + `funding.parquet` (plus `hl_candles_reservoir/` with additional assets). |
| `hl-ticks-2026-08.zip` | Multi-exchange quote snapshots, 2026-08-17 → 2026-08-22 (6 days). One gzipped CSV per day. Columns: `ts, pair, exchange, mark, mid, bid, ask, bid_size, ask_size, last, funding, status`. Exchanges: Hyperliquid, Binance, Asterdex, Paradex, Aevo, GRVT. |
| `hl-trades-bbo.zip` | Trade prints and BBO snapshots as zstd-compressed parquet. `trades.parquet` — 16.7M trade prints across 7 exchanges. `bbo/exchange=<NAME>/data_0.parquet` — 71.1M best-bid/offer snapshots partitioned by exchange. `trades/` — the same trades partitioned per-day per-exchange. Mostly 2026-08-17 → 2026-08-22, sparse rows back to 2026-08-05. |
| `hl-reference.zip` | Reference metadata: per-exchange asset lists (`exchange_assets/`), max leverage tables (`leverage/`), Hyperliquid margin tables, backfill checkpoint. |

## Schemas

`candles.parquet`: `timestamp, timestamp_end, coin, interval, open, high, low, close, volume, trade_count` — timestamps in ms (UTC).

`funding.parquet`: `timestamp, coin, funding_rate, premium`.

`trades.parquet`: `exchange, symbol, coin, trade_id, price, size, side, ts, fetched_at` — `ts` in epoch seconds (float).

`bbo` parquets: `exchange, coin, bid, ask, bid_size, ask_size, mid, mark, last, funding, ts`.

## Reading

```python
import pandas as pd
df = pd.read_parquet("hl_candles/BTC/candles.parquet")

# or query everything at once with duckdb:
import duckdb
duckdb.sql("SELECT * FROM 'bbo/exchange=*/data_0.parquet' LIMIT 5")
```

## Provenance

Self-collected via public exchange WebSocket/REST feeds, Aug 2026.
Candle history backfilled to 2025-08-01 where available.
