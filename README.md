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
| `iCRD` | 2000-01 | 321 | 8.81% | 4.86% | 1.36 | 1.81 | -17.51% | 0.17 | 0.28 | 7.29% | 5.55% | 0.00 | 7.94% | 4.17% | 2.98% |
| `iCRDCBA` | 2000-01 | 321 | 9.14% | 5.20% | 1.34 | 1.76 | -18.30% | 0.15 | 0.19 | 7.81% | 6.04% | -0.04 | 10.42% | 7.10% | 4.92% |
| `iCRDDSS` | 2000-01 | 321 | 8.57% | 6.43% | 1.01 | 1.33 | -24.51% | 0.24 | 0.32 | 6.45% | 4.87% | 0.12 | 9.32% | 4.85% | 2.29% |
| `iCRDLSC` | 2004-01 | 273 | 6.77% | 5.40% | 0.91 | 1.25 | -24.42% | 0.21 | 0.34 | 4.34% | 2.89% | 0.10 | 7.18% | 2.74% | 2.21% |
| `iCRDMBS` | 2000-01 | 321 | 11.33% | 7.02% | 1.29 | 1.61 | -19.89% | 0.10 | 0.05 | 10.60% | 8.66% | -0.13 | 8.08% | 3.67% | 2.59% |
| `iCRDMST` | 2000-01 | 321 | 8.63% | 4.99% | 1.29 | 1.73 | -17.24% | 0.17 | 0.27 | 7.09% | 5.36% | 0.01 | 7.71% | 4.54% | 4.46% |
| `iCRDREL` | 2001-01 | 309 | 7.58% | 3.91% | 1.42 | 1.94 | -13.72% | 0.06 | 0.05 | 7.06% | 5.26% | -0.11 | 7.47% | 4.41% | 2.09% |
| `iEMN` | 2001-01 | 309 | 8.46% | 4.14% | 1.57 | 2.04 | -5.53% | 0.10 | 0.13 | 7.52% | 5.79% | -0.03 | 8.46% | 6.21% | 3.23% |
| `iEQD` | 2000-01 | 321 | 9.65% | 7.03% | 1.07 | 1.37 | -18.50% | 0.37 | 0.65 | 6.24% | 4.95% | 0.27 | 13.53% | 9.41% | 6.76% |
| `iEQDALS` | 2003-01 | 285 | 10.51% | 8.71% | 1.00 | 1.21 | -29.47% | 0.36 | 0.36 | 6.21% | 5.04% | 0.27 | 14.82% | 10.73% | 9.37% |
| `iEQDELS` | 2000-01 | 321 | 7.38% | 8.33% | 0.66 | 0.89 | -19.81% | 0.33 | 0.36 | 4.56% | 3.19% | 0.26 | 10.52% | 8.28% | 6.75% |
| `iEQDGLS` | 2000-01 | 321 | 9.79% | 7.10% | 1.09 | 1.38 | -19.02% | 0.32 | 0.47 | 6.87% | 5.48% | 0.21 | 15.17% | 10.66% | 7.28% |
| `iEQDULS` | 2000-01 | 321 | 9.55% | 7.62% | 0.98 | 1.25 | -18.53% | 0.43 | 0.72 | 5.66% | 4.49% | 0.33 | 12.19% | 7.25% | 4.74% |
| `iEQS` | 2000-01 | 321 | 12.91% | 8.43% | 1.27 | 1.53 | -20.57% | 0.40 | 0.52 | 9.24% | 8.00% | 0.24 | 20.02% | 21.49% | 11.46% |
| `iEQSCON` | 2001-01 | 309 | 7.68% | 9.94% | 0.62 | 0.77 | -19.25% | 0.30 | 0.21 | 5.04% | 3.73% | 0.27 | 5.25% | -4.09% | -0.41% |
| `iEQSEUI` | 2000-01 | 321 | 12.24% | 8.96% | 1.13 | 1.37 | -25.13% | 0.35 | 0.36 | 9.11% | 7.75% | 0.17 | 11.78% | 10.87% | 5.86% |
| `iEQSFIN` | 2000-01 | 321 | 11.90% | 9.63% | 1.02 | 1.24 | -22.57% | 0.40 | 0.39 | 8.44% | 7.17% | 0.22 | 14.53% | 9.97% | 4.87% |
| `iEQSHLT` | 2000-01 | 321 | 14.99% | 11.71% | 1.10 | 1.28 | -25.95% | 0.42 | 0.29 | 11.47% | 10.24% | 0.23 | 19.90% | 24.34% | 3.57% |
| `iEQSREI` | 2010-01 | 201 | 9.59% | 8.22% | 0.96 | 1.17 | -11.45% | 0.42 | 0.54 | 3.53% | 2.58% | 0.34 | 6.31% | 4.38% | 3.01% |
| `iEQSTMT` | 2002-01 | 297 | 10.52% | 9.30% | 0.94 | 1.13 | -27.51% | 0.42 | 0.46 | 6.10% | 5.06% | 0.34 | 24.52% | 24.45% | 21.77% |
| `iEVD` | 2000-01 | 321 | 9.18% | 6.86% | 1.04 | 1.34 | -21.24% | 0.30 | 0.44 | 6.49% | 5.04% | 0.17 | 11.15% | 8.14% | 5.67% |
| `iEVDESS` | 2000-01 | 321 | 11.38% | 8.56% | 1.08 | 1.33 | -16.54% | 0.38 | 0.44 | 8.02% | 6.71% | 0.22 | 10.69% | 8.21% | 5.21% |
| `iEVDMER` | 2000-01 | 321 | 9.53% | 6.88% | 1.09 | 1.38 | -13.63% | 0.13 | 0.08 | 8.47% | 6.66% | -0.07 | 8.18% | 7.36% | 4.87% |
| `iEVDMEV` | 2000-01 | 321 | 9.11% | 7.04% | 1.00 | 1.29 | -22.28% | 0.31 | 0.44 | 6.36% | 4.93% | 0.19 | 11.82% | 8.34% | 5.93% |
| `iGBM` | 2000-01 | 321 | 7.88% | 5.88% | 0.98 | 1.34 | -9.43% | 0.01 | 0.00 | 7.92% | 5.84% | -0.12 | 7.36% | 9.50% | 6.88% |
| `iGBMCOM` | 2006-01 | 249 | 12.27% | 8.58% | 1.19 | 1.43 | -15.14% | 0.01 | 0.00 | 12.52% | 10.61% | -0.17 | 2.01% | 4.58% | 1.55% |
| `iGBMDSC` | 2001-01 | 309 | 9.59% | 5.53% | 1.36 | 1.74 | -11.62% | 0.08 | 0.04 | 8.93% | 7.14% | -0.10 | 9.25% | 7.07% | 3.46% |
| `iGBMMMA` | 2000-01 | 321 | 9.80% | 5.04% | 1.50 | 1.94 | -5.50% | -0.03 | 0.01 | 10.27% | 8.06% | -0.24 | 5.40% | 3.61% | 1.12% |
| `iGBMQNT` | 2000-01 | 321 | 6.58% | 6.95% | 0.67 | 0.95 | -10.80% | -0.03 | 0.01 | 7.17% | 5.01% | -0.15 | 5.87% | 12.39% | 10.75% |
| `iGBMRPM` | 2008-01 | 225 | 8.21% | 10.16% | 0.69 | 0.81 | -24.51% | 0.34 | 0.28 | 4.42% | 3.43% | 0.24 | 10.70% | 15.40% | 13.43% |
| `iHFC` | 2000-01 | 321 | 9.23% | 4.79% | 1.47 | 1.93 | -12.82% | 0.20 | 0.39 | 7.41% | 5.74% | 0.06 | 10.80% | 9.88% | 6.70% |
| `iHFCEW` | 2000-01 | 321 | 9.79% | 5.21% | 1.46 | 1.88 | -14.01% | 0.24 | 0.47 | 7.59% | 6.00% | 0.10 | 11.50% | 10.30% | 6.95% |
| `iMFT` | 2000-01 | 321 | 7.43% | 9.34% | 0.61 | 0.80 | -15.36% | -0.10 | 0.03 | 8.89% | 6.57% | -0.19 | 3.48% | 18.17% | 13.72% |
| `iMFTNTF` | 2000-01 | 321 | 6.81% | 7.22% | 0.68 | 0.94 | -13.30% | -0.12 | 0.07 | 8.30% | 5.95% | -0.28 | 3.45% | 18.13% | 18.76% |
| `iMFTTFO` | 2000-01 | 321 | 7.61% | 11.76% | 0.52 | 0.65 | -18.61% | -0.10 | 0.02 | 9.31% | 6.98% | -0.16 | 3.98% | 19.95% | 13.79% |
| `iMST` | 2000-01 | 321 | 9.07% | 4.19% | 1.65 | 2.17 | -15.05% | 0.13 | 0.23 | 7.85% | 6.04% | -0.04 | 10.11% | 9.39% | 6.10% |
| `iQNT` | 2003-01 | 285 | 6.28% | 4.55% | 0.97 | 1.38 | -19.18% | 0.13 | 0.17 | 4.72% | 3.14% | -0.01 | 10.95% | 9.78% | 6.42% |
| `iRVI` | 2000-01 | 321 | 8.66% | 3.39% | 1.93 | 2.56 | -11.42% | 0.10 | 0.18 | 7.78% | 5.89% | -0.07 | 8.74% | 7.44% | 4.71% |
| `iVOL` | 2000-01 | 321 | 8.43% | 4.99% | 1.28 | 1.69 | -4.64% | -0.06 | 0.04 | 9.20% | 6.96% | -0.27 | 3.42% | 4.27% | 3.94% |
| **`SP500TR`** | 2000-01 | 321 | 8.33% | 15.15% | - | - | -50.95% | 1.00 | 1.00 | - | - | - | - | - | - |

*Since inception, through 2026-09. Full precision and the trailing 36-month window are in [`index_statistics.csv`](index_statistics.csv).*

- **Sharpe** is annualised and excess of the risk-free rate (3-month U.S. T-Bill, FRED `DGS3MO`), consistent with PivotalPath's published factsheets.
- **Return/vol** is the raw ratio of annualised return to annualised volatility, with *no* risk-free subtraction. It is not a Sharpe ratio and should not be quoted as one.
- **Alpha is published twice, under two names.** `alpha_sp500_ann_*` is the intercept of a **raw**-return regression of the index on S&P 500 total return, with no risk-free subtraction anywhere. `jensens_alpha_sp500_ann_*` subtracts the risk-free rate from **both** the index and the market, per the standard Jensen definition, using the same risk-free series as `sharpe_ratio`. They are different numbers; quote the one you mean.
- Each alpha ships with the **beta** and **R²** from its own fit — `beta_sp500_*` / `r_squared_sp500_*` for the raw regression, `beta_sp500_excess_*` / `r_squared_sp500_excess_*` for the excess one. Read them together: where R² is low the index is largely unexplained by equity market moves and the alpha carries little meaning.
- **Downside capture** answers what `max_drawdown` cannot. A drawdown is the index's own worst run, whenever it happened; `downside_capture_sp500_*` is the mean index return in months the S&P **fell**, over the mean S&P return in those months — below 1.00 means the index fell less than the market did. `up`/`down` capture ship with `down_months_count_*`, because a ratio over few months is noise, and with `return_worst10_sp500_*` alongside `sp500_worst10_mean_*` — the index and the market across the same ten worst market months.
- The S&P 500 itself is in [`benchmark_reference.csv`](benchmark_reference.csv) (return, volatility, drawdown, both windows) and its calendar years are in `cy_returns.csv` under id `SP500TR`. A capture ratio is a comparison, so the thing compared against is published too.
- **Trailing periods** follow one rule, stated in every column name: periods longer than a year are **annualised** (`ann_return_2y`, `_3y`, `_5y`, `_10y`), periods of a year or less are **cumulative** (`cum_return_1y`, `cum_return_ytd`, `cum_return_mtd`). A window is published only when every month in it is present; otherwise the cell is **empty**, never zero.
- **Quarters** are published under their calendar name (`q_2026Q3`), never a relative one. A column named for a quarter means the same thing permanently; the set rolls forward as quarters complete, so read the header rather than assuming a fixed position.
- **Calendar-year returns** are in [`cy_returns.csv`](cy_returns.csv), one row per index-year. The running year carries `partial=1` and its `months` count, so it can never be read as a full year.
- **Quarterly returns** are in [`quarterly_returns.csv`](quarterly_returns.csv), the same shape and the same conventions, one row per index-quarter over the full history - not only the four quarters named in the table above. `quarter` is the calendar name (`2026Q2`) and the running quarter carries `partial=1` with its `months` count, so a quarter-to-date figure can never be read as a complete quarter. The S&P 500 is here too, under id `SP500TR`.
- Exactly two figures use **excess** returns: `sharpe_ratio` and `jensens_alpha_sp500_ann_*`, both against the same risk-free series. Everything else here — trailing periods, calendar years, capture ratios and `alpha_sp500_ann_*` — is on **raw** returns.

<!-- STATS:END -->
