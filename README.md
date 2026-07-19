# Brent Volatility Forecasting — Hormuz Crisis 2026

Forecasts Brent crude oil daily volatility across the 2026 Strait of Hormuz crisis using GARCH(1,1), LSTM, and a GARCH+LSTM hybrid. WTI is included as a robustness check.

## Files

```
Brent_Vol.ipynb                           — main notebook
BRENT Oil Futures Historical Data.csv     — Brent daily OHLC futures
Crude Oil WTI Futures Historical Data.csv — WTI daily OHLC futures
```

## Data

Daily OHLC futures prices from Investing.com, 5 Jan 2016 – 10 Jul 2026. Train/test split at the crisis onset: everything before 28 Feb 2026 is training, everything from 2 Mar 2026 onward is the test window (~94 observations).

## Models

All four models use the same rolling one-step-ahead design: refit every 5 trading days, predict daily.

| Model | Description |
|---|---|
| GARCH (rolling) | GARCH(1,1) on log returns, order confirmed BIC-optimal |
| LSTM (ε²) | 2-layer LSTM, 128 units, 22-day lookback, targets squared demeaned returns |
| Hybrid | GARCH forecast + LSTM correction of GARCH residuals, floored at 20% of GARCH |
| LSTM (Vol/OHLC) | Same LSTM but targets a Rogers–Satchell/Yang–Zhang range estimator |

## Results

Evaluated on the crisis test window by RMSE, MAE, and QLIKE (primary metric).

| Series | Model | RMSE | MAE | QLIKE |
|--------|-------|------|-----|-------|
| Brent | GARCH | 0.003935 | 0.002146 | −5.1041 |
| Brent | Hybrid | 0.004032 | 0.002202 | −5.1033 |
| Brent | LSTM (ε²) | 0.004222 | 0.001915 | −3.8218 |
| Brent | LSTM (Vol/OHLC) | 0.008289 | 0.002759 | −2.5344 |
| WTI | GARCH | 0.004516 | 0.002359 | −4.9703 |
| WTI | LSTM (ε²) | 0.004636 | 0.002023 | −4.5218 |
| WTI | LSTM (Vol/OHLC) | 0.007684 | 0.002490 | −3.8189 |
| WTI | Hybrid | 0.005544 | 0.002965 | −3.4299 |

GARCH wins on both series by QLIKE. The hybrid is near identical to GARCH on Brent but weakest on WTI. The standalone LSTM gets the lowest MAE on both series but poor QLIKE. It predicts a near constant low value that misses the large spikes.
