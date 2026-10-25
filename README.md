# Order Book Analysis

Predict 10-second mid-price returns from top-of-book quotes and trades.

- Inspect cleaning, intraday activity, VWAP, spreads, trade direction and five-minute returns.
- Build ten quote and trade-flow features on a one-second grid.
- Compare zero and mean forecasts, lag regression, Ridge and random forest.

Random forest lowers test RMSE by 5.05% versus zero; zero has the lowest MAE. The study covers one day, with 49 stocks represented in testing.

## Run

Use Python 3.12. From this project folder:

```sh
python3.12 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
jupyter lab intraday_orderbook_research.ipynb
```

Run all cells from top to bottom. The notebook includes saved tables and figures for reading on GitHub.

To execute without opening Jupyter:

```sh
jupyter nbconvert --execute --to notebook --inplace --ExecutePreprocessor.timeout=600 intraday_orderbook_research.ipynb
```

## Files

- `intraday_orderbook_research.ipynb`: complete analysis and model comparison.
- `data/market_quotes.csv.gz`: timestamp, stock, best bid/ask prices and sizes.
- `data/market_trades.csv.gz`: timestamp, stock, trade price and quantity.
- `requirements.txt`: pinned analysis packages and Jupyter tools. Pip installs their dependencies.

Quotes: `recv_time`, `symbol`, `bid_price`, `bid_size`, `ask_price`, `ask_size`.

Trades: `recv_time`, `symbol`, `trade_price`, `trade_qty`.

Timestamps are Unix microseconds, interpreted in Shanghai time. Prices are assumed to be CNY and quantities shares. Compressed inputs are read directly by pandas.

## Method

Use 09:30–11:30 and 13:00–15:00 for model sampling. Match quotes backward in time and reject snapshots older than five seconds. Keep missing quotes separate from zero trading volume.

All stocks share a chronological 60%/40% train/test split, with inner validation for tuning. Purge overlapping ten-second targets at both boundaries and fit scaling on training data only.

One day cannot establish stability across days. Tick direction estimates aggressor side; quoted spreads and model errors do not establish executable trading profits.

Asset IDs are pseudonyms and the calendar date is shifted. Intraday timestamps, event order, prices and quantities are preserved.
