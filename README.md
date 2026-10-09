# PivotalPath Public Indices

> Institutional-quality hedge fund index returns — monthly, net of fees, free and machine-readable.

[PivotalPath](https://www.pivotalpath.com) is a hedge fund research and
due-diligence firm. Since 2013 we've built unbiased, institutionally-relevant
data on the hedge fund industry on behalf of allocators and managers. This
repository is the **public expression of that research**: the monthly return
series for our headline hedge fund indices, free to use with attribution.

The indices track funds reporting **monthly performance, net of all fees, in
USD**, with a minimum track record of 18 months and a minimum AUM of $50mm.
Constituents are fixed at the end of each calendar year for the following year;
indices are rebalanced monthly.

## What's in this repo

| File | What it is |
| --- | --- |
| [`index_catalog.csv`](index_catalog.csv) | One row per index — id, display name, and a full description. |
| [`index_return.csv`](index_return.csv) | The monthly return series for every index, in long (tidy) format. |
| [`index_statistics.csv`](index_statistics.csv) | Summary statistics per index, derived from `index_return.csv`. |
| [`cy_returns.csv`](cy_returns.csv) | Calendar-year returns, one row per index-year. |
| [`benchmark_reference.csv`](benchmark_reference.csv) | The S&P 500's own return, volatility and drawdown over the same windows. |
| [`quarterly_returns.csv`](quarterly_returns.csv) | Quarterly returns, one row per index-quarter. |
| [`metadata.json`](metadata.json) | `as_of`, `updated_at`, refresh cadence and expected reporting lag. |

## Data dictionary

### `index_catalog.csv`

| Column | Type | Description |
| --- | --- | --- |
| `id` | string | Stable index identifier (e.g. `iHFC`). Join key to `index_return.csv`. |
| `name` | string | Human-readable index name. |
| `abstract` | string | Full methodology description for the index. |

### `index_return.csv`

| Column | Type | Description |
| --- | --- | --- |
| `date` | string `YYYY-MM` | Calendar month of the return. |
| `id` | string | Index identifier — joins to `index_catalog.csv`. |
| `mtd` | number | Month-to-date return, **net of fees**, as a **decimal** (`0.0085` = +0.85%). |

> [!NOTE]
> Returns are **decimals, not percentages**, and the data is in **long
> format** (one row per index-month). History runs from **2000-01** for nine
> of the ten published indices. `iQNT` begins **2003-01**, the point at which
> its underlying sub-groups first reached viable constituent counts. Exact
> per-index coverage is in `index_statistics.csv` (`coverage_start`,
> `months`).

## Indices

| id | Index | Focus |
| --- | --- | --- |
| `iHFC` | PivotalPath Composite | All strategies and geographies (the headline index). |
| `iCRD` | PivotalPath Credit | Strategies trading primarily in credit markets. |
| `iEQD` | PivotalPath Equity Diversified | Diversified equity long/short across geographies. |
| `iQNT` | PivotalPath Equity Quant | Systematic, quantitative equity long/short. |
| `iEQS` | PivotalPath Equity Sector | Sector-focused equity (TMT, Healthcare, Energy, Financials, …). |
| `iEVD` | PivotalPath Event Driven | Merger arbitrage, special situations, multi-event. |
| `iGBM` | PivotalPath Global Macro | Discretionary and systematic macro across asset classes. |
| `iMFT` | PivotalPath Managed Futures | Trend-following / CTA across futures markets. |
| `iMST` | PivotalPath Multi-Strategy | Funds combining multiple strategies across asset classes. |
| `iVOL` | PivotalPath Volatility Trading | Volatility and tail-risk strategies. |

## Quick start

```python
import pandas as pd

catalog = pd.read_csv("index_catalog.csv")
returns = pd.read_csv("index_return.csv", parse_dates=["date"], date_format="%Y-%m")

# Join names onto the return series
df = returns.merge(catalog[["id", "name"]], on="id")

# Example: the Composite index's most recent months
composite = df[df["id"] == "iHFC"].sort_values("date")
print(composite.tail())

# Returns are decimals — convert a series to a cumulative growth curve
growth = (1 + composite.set_index("date")["mtd"]).cumprod()
print(f"Composite total return: {growth.iloc[-1] - 1:.1%}")
```

Wide format (date × index), if you'd rather have one column per index:

```python
wide = returns.pivot(index="date", columns="id", values="mtd")
```

## How to cite

> Source: PivotalPath. *PivotalPath [Index Name] Hedge Fund Index*, net of fees.
> https://www.pivotalpath.com

## The data is the tip of the iceberg

Allocators use this same research to screen managers, run alpha/beta and
cross-sectional analysis, and construct portfolios; managers use it to benchmark
against anonymized peer distributions. See the full platform →
[pivotalpath.com](https://www.pivotalpath.com).

## License

Licensed under [Creative Commons Attribution 4.0 International (CC BY 4.0)](LICENSE)
— free to use, share, and adapt, including commercially, as long as you give
appropriate credit to PivotalPath.

## Index statistics

<!-- STATS:START -->

| Index | From | Months | Ann. return | Ann. vol | Sharpe | Return/vol | Max DD | Beta (S&P) | R² | α raw | Jensen's α | Down capt. | 3Y ann. | 1Y cum. | YTD cum. |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| `iCRD` | 2000-01 | 320 | 8.87% | 4.86% | 1.38 | 1.83 | -17.51% | 0.17 | 0.27 | 7.34% | 5.61% | 0.00 | 8.41% | 5.83% | 3.73% |
| `iCRDCBA` | 2000-01 | 320 | 9.22% | 5.19% | 1.36 | 1.78 | -18.30% | 0.15 | 0.19 | 7.89% | 6.12% | -0.04 | 11.05% | 10.51% | 6.19% |
| `iCRDDSS` | 2000-01 | 320 | 8.63% | 6.43% | 1.02 | 1.34 | -24.51% | 0.24 | 0.32 | 6.51% | 4.93% | 0.12 | 9.54% | 7.27% | 3.21% |
| `iCRDLSC` | 2004-01 | 272 | 6.84% | 5.40% | 0.92 | 1.27 | -24.42% | 0.21 | 0.34 | 4.39% | 2.95% | 0.09 | 7.65% | 4.79% | 3.14% |
| `iCRDMBS` | 2000-01 | 320 | 11.38% | 7.03% | 1.29 | 1.62 | -19.89% | 0.10 | 0.05 | 10.65% | 8.72% | -0.14 | 8.37% | 4.42% | 2.99% |
| `iCRDMST` | 2000-01 | 320 | 8.68% | 4.99% | 1.31 | 1.74 | -17.24% | 0.17 | 0.27 | 7.14% | 5.42% | 0.01 | 8.21% | 6.02% | 5.03% |
| `iCRDREL` | 2001-01 | 308 | 7.65% | 3.90% | 1.45 | 1.96 | -13.72% | 0.06 | 0.05 | 7.14% | 5.34% | -0.12 | 8.15% | 6.15% | 3.33% |
| `iEMN` | 2001-01 | 308 | 8.48% | 4.15% | 1.57 | 2.04 | -5.53% | 0.10 | 0.13 | 7.54% | 5.82% | -0.03 | 8.44% | 7.69% | 3.04% |
| `iEQD` | 2000-01 | 320 | 9.74% | 7.03% | 1.09 | 1.38 | -18.50% | 0.37 | 0.65 | 6.30% | 5.02% | 0.27 | 13.61% | 12.82% | 8.15% |
| `iEQDALS` | 2003-01 | 284 | 10.60% | 8.74% | 1.00 | 1.21 | -29.47% | 0.36 | 0.36 | 6.27% | 5.11% | 0.27 | 14.91% | 14.99% | 10.59% |
| `iEQDELS` | 2000-01 | 320 | 7.45% | 8.34% | 0.67 | 0.89 | -19.81% | 0.33 | 0.36 | 4.61% | 3.25% | 0.26 | 10.57% | 10.70% | 7.86% |
| `iEQDGLS` | 2000-01 | 320 | 9.87% | 7.10% | 1.10 | 1.39 | -19.02% | 0.32 | 0.48 | 6.93% | 5.54% | 0.20 | 15.25% | 14.99% | 8.47% |
| `iEQDULS` | 2000-01 | 320 | 9.65% | 7.61% | 1.00 | 1.27 | -18.53% | 0.43 | 0.72 | 5.75% | 4.58% | 0.33 | 12.36% | 9.60% | 6.66% |
| `iEQS` | 2000-01 | 320 | 13.04% | 8.43% | 1.28 | 1.55 | -20.57% | 0.40 | 0.52 | 9.35% | 8.11% | 0.24 | 20.03% | 29.30% | 13.69% |
| `iEQSCON` | 2001-01 | 308 | 7.57% | 9.94% | 0.61 | 0.76 | -19.25% | 0.30 | 0.21 | 4.91% | 3.61% | 0.28 | 4.18% | -7.43% | -3.63% |
| `iEQSEUI` | 2000-01 | 320 | 12.38% | 8.95% | 1.14 | 1.38 | -25.13% | 0.35 | 0.36 | 9.24% | 7.89% | 0.17 | 12.23% | 16.79% | 8.50% |
| `iEQSFIN` | 2000-01 | 320 | 12.01% | 9.63% | 1.03 | 1.25 | -22.57% | 0.40 | 0.39 | 8.53% | 7.27% | 0.22 | 14.78% | 11.56% | 6.80% |
| `iEQSHLT` | 2000-01 | 320 | 15.35% | 11.63% | 1.13 | 1.32 | -25.95% | 0.41 | 0.29 | 11.80% | 10.57% | 0.21 | 21.95% | 43.18% | 11.10% |
| `iEQSREI` | 2010-01 | 200 | 9.93% | 8.15% | 1.01 | 1.22 | -11.45% | 0.42 | 0.54 | 3.83% | 2.88% | 0.32 | 7.43% | 8.83% | 7.56% |
| `iEQSTMT` | 2002-01 | 296 | 10.46% | 9.32% | 0.94 | 1.12 | -27.51% | 0.42 | 0.46 | 6.02% | 4.99% | 0.35 | 22.70% | 26.04% | 19.22% |
| `iEVD` | 2000-01 | 320 | 9.26% | 6.86% | 1.05 | 1.35 | -21.24% | 0.30 | 0.44 | 6.55% | 5.11% | 0.17 | 11.37% | 11.10% | 6.93% |
| `iEVDESS` | 2000-01 | 320 | 11.55% | 8.55% | 1.10 | 1.35 | -16.54% | 0.38 | 0.44 | 8.17% | 6.87% | 0.21 | 11.46% | 12.27% | 8.80% |
| `iEVDMER` | 2000-01 | 320 | 9.58% | 6.89% | 1.10 | 1.39 | -13.63% | 0.13 | 0.09 | 8.51% | 6.71% | -0.07 | 8.77% | 8.41% | 5.32% |
| `iEVDMEV` | 2000-01 | 320 | 9.18% | 7.04% | 1.01 | 1.30 | -22.28% | 0.31 | 0.44 | 6.42% | 4.99% | 0.18 | 11.88% | 11.44% | 6.99% |
| `iGBM` | 2000-01 | 320 | 7.87% | 5.88% | 0.98 | 1.34 | -9.43% | 0.01 | 0.00 | 7.91% | 5.84% | -0.12 | 7.63% | 10.67% | 5.95% |
| `iGBMCOM` | 2006-01 | 248 | 12.28% | 8.60% | 1.19 | 1.43 | -15.14% | 0.01 | 0.00 | 12.55% | 10.64% | -0.17 | 2.06% | 5.51% | 0.91% |
| `iGBMDSC` | 2001-01 | 308 | 9.65% | 5.53% | 1.37 | 1.74 | -11.62% | 0.08 | 0.04 | 8.99% | 7.20% | -0.11 | 9.85% | 10.01% | 4.00% |
| `iGBMMMA` | 2000-01 | 320 | 9.85% | 5.05% | 1.51 | 1.95 | -5.50% | -0.03 | 0.01 | 10.33% | 8.13% | -0.24 | 6.11% | 4.76% | 1.71% |
| `iGBMQNT` | 2000-01 | 320 | 6.49% | 6.94% | 0.66 | 0.93 | -10.80% | -0.03 | 0.01 | 7.08% | 4.92% | -0.14 | 5.56% | 11.74% | 7.61% |
| `iGBMRPM` | 2008-01 | 224 | 8.15% | 10.18% | 0.68 | 0.80 | -24.51% | 0.34 | 0.28 | 4.33% | 3.35% | 0.25 | 10.79% | 15.40% | 11.49% |
| `iHFC` | 2000-01 | 320 | 9.28% | 4.79% | 1.48 | 1.94 | -12.82% | 0.20 | 0.39 | 7.45% | 5.78% | 0.06 | 10.96% | 12.38% | 7.10% |
| `iHFCEW` | 2000-01 | 320 | 9.85% | 5.21% | 1.47 | 1.89 | -14.01% | 0.24 | 0.47 | 7.64% | 6.06% | 0.10 | 11.67% | 13.25% | 7.69% |
| `iMFT` | 2000-01 | 320 | 7.31% | 9.33% | 0.59 | 0.78 | -15.36% | -0.10 | 0.03 | 8.77% | 6.45% | -0.18 | 3.18% | 17.69% | 9.64% |
| `iMFTNTF` | 2000-01 | 320 | 6.66% | 7.19% | 0.67 | 0.93 | -13.30% | -0.12 | 0.07 | 8.15% | 5.81% | -0.27 | 2.91% | 14.16% | 13.86% |
| `iMFTTFO` | 2000-01 | 320 | 7.49% | 11.76% | 0.51 | 0.64 | -18.61% | -0.10 | 0.02 | 9.19% | 6.87% | -0.15 | 3.70% | 20.86% | 9.75% |
| `iMST` | 2000-01 | 320 | 9.09% | 4.19% | 1.65 | 2.17 | -15.05% | 0.13 | 0.23 | 7.87% | 6.07% | -0.04 | 10.26% | 10.85% | 5.89% |
| `iQNT` | 2003-01 | 284 | 6.18% | 4.54% | 0.96 | 1.36 | -19.18% | 0.13 | 0.18 | 4.61% | 3.04% | -0.01 | 10.48% | 6.54% | 3.54% |
| `iRVI` | 2000-01 | 320 | 8.69% | 3.39% | 1.94 | 2.56 | -11.42% | 0.10 | 0.18 | 7.80% | 5.92% | -0.07 | 8.89% | 9.03% | 4.66% |
| `iVOL` | 2000-01 | 320 | 8.45% | 5.00% | 1.28 | 1.69 | -4.64% | -0.06 | 0.04 | 9.22% | 6.99% | -0.27 | 3.67% | 4.93% | 3.57% |
| **`SP500TR`** | 2000-01 | 320 | 8.37% | 15.17% | - | - | -50.95% | 1.00 | 1.00 | - | - | - | - | - | - |

*Since inception, through 2026-08. Full precision and the trailing 36-month window are in [`index_statistics.csv`](index_statistics.csv).*

- **Sharpe** is annualised and excess of the risk-free rate (3-month U.S. T-Bill, FRED `DGS3MO`), consistent with PivotalPath's published factsheets.
- **Return/vol** is the raw ratio of annualised return to annualised volatility, with *no* risk-free subtraction. It is not a Sharpe ratio and should not be quoted as one.
- **Alpha is published twice, under two names.** `alpha_sp500_ann_*` is the intercept of a **raw**-return regression of the index on S&P 500 total return, with no risk-free subtraction anywhere. `jensens_alpha_sp500_ann_*` subtracts the risk-free rate from **both** the index and the market, per the standard Jensen definition, using the same risk-free series as `sharpe_ratio`. They are different numbers; quote the one you mean.
- Each alpha ships with the **beta** and **R²** from its own fit — `beta_sp500_*` / `r_squared_sp500_*` for the raw regression, `beta_sp500_excess_*` / `r_squared_sp500_excess_*` for the excess one. Read them together: where R² is low the index is largely unexplained by equity market moves and the alpha carries little meaning.
- **Downside capture** answers what `max_drawdown` cannot. A drawdown is the index's own worst run, whenever it happened; `downside_capture_sp500_*` is the mean index return in months the S&P **fell**, over the mean S&P return in those months — below 1.00 means the index fell less than the market did. `up`/`down` capture ship with `down_months_count_*`, because a ratio over few months is noise, and with `return_worst10_sp500_*` alongside `sp500_worst10_mean_*` — the index and the market across the same ten worst market months.
- The S&P 500 itself is in [`benchmark_reference.csv`](benchmark_reference.csv) (return, volatility, drawdown, both windows) and its calendar years are in `cy_returns.csv` under id `SP500TR`. A capture ratio is a comparison, so the thing compared against is published too.
- **Trailing periods** follow one rule, stated in every column name: periods longer than a year are **annualised** (`ann_return_2y`, `_3y`, `_5y`, `_10y`), periods of a year or less are **cumulative** (`cum_return_1y`, `cum_return_ytd`, `cum_return_mtd`). A window is published only when every month in it is present; otherwise the cell is **empty**, never zero.
- **Quarters** are published under their calendar name (`q_2026Q2`), never a relative one. A column named for a quarter means the same thing permanently; the set rolls forward as quarters complete, so read the header rather than assuming a fixed position.
- **Calendar-year returns** are in [`cy_returns.csv`](cy_returns.csv), one row per index-year. The running year carries `partial=1` and its `months` count, so it can never be read as a full year.
- **Quarterly returns** are in [`quarterly_returns.csv`](quarterly_returns.csv), the same shape and the same conventions, one row per index-quarter over the full history - not only the four quarters named in the table above. `quarter` is the calendar name (`2026Q2`) and the running quarter carries `partial=1` with its `months` count, so a quarter-to-date figure can never be read as a complete quarter. The S&P 500 is here too, under id `SP500TR`.
- Exactly two figures use **excess** returns: `sharpe_ratio` and `jensens_alpha_sp500_ann_*`, both against the same risk-free series. Everything else here — trailing periods, calendar years, capture ratios and `alpha_sp500_ann_*` — is on **raw** returns.

<!-- STATS:END -->
