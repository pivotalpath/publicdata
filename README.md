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
| `iCRD` | 2000-01 | 319 | 8.89% | 4.86% | 1.38 | 1.83 | -17.51% | 0.17 | 0.28 | 7.37% | 5.65% | 0.00 | 8.55% | 6.49% | 3.52% |
| `iCRDCBA` | 2000-01 | 319 | 9.26% | 5.20% | 1.37 | 1.78 | -18.30% | 0.15 | 0.19 | 7.93% | 6.17% | -0.04 | 11.23% | 12.20% | 6.42% |
| `iCRDDSS` | 2000-01 | 319 | 8.64% | 6.44% | 1.02 | 1.34 | -24.51% | 0.24 | 0.32 | 6.53% | 4.96% | 0.12 | 9.47% | 8.46% | 2.64% |
| `iCRDLSC` | 2004-01 | 271 | 6.86% | 5.41% | 0.93 | 1.27 | -24.42% | 0.21 | 0.34 | 4.42% | 2.99% | 0.09 | 7.74% | 5.39% | 2.92% |
| `iCRDMBS` | 2000-01 | 319 | 11.39% | 7.04% | 1.29 | 1.62 | -19.89% | 0.10 | 0.05 | 10.67% | 8.74% | -0.14 | 8.48% | 4.46% | 2.29% |
| `iCRDMST` | 2000-01 | 319 | 8.69% | 5.00% | 1.31 | 1.74 | -17.24% | 0.17 | 0.27 | 7.16% | 5.45% | 0.01 | 8.29% | 6.39% | 4.70% |
| `iCRDREL` | 2001-01 | 307 | 7.66% | 3.91% | 1.45 | 1.96 | -13.72% | 0.06 | 0.05 | 7.15% | 5.36% | -0.12 | 8.34% | 6.63% | 2.94% |
| `iEMN` | 2001-01 | 307 | 8.50% | 4.16% | 1.57 | 2.04 | -5.53% | 0.10 | 0.13 | 7.56% | 5.85% | -0.03 | 8.74% | 8.67% | 2.82% |
| `iEQD` | 2000-01 | 319 | 9.72% | 7.04% | 1.08 | 1.38 | -18.50% | 0.37 | 0.65 | 6.32% | 5.04% | 0.27 | 13.06% | 13.96% | 6.94% |
| `iEQDALS` | 2003-01 | 283 | 10.58% | 8.73% | 1.00 | 1.21 | -29.47% | 0.36 | 0.36 | 6.29% | 5.13% | 0.26 | 14.05% | 18.27% | 9.23% |
| `iEQDELS` | 2000-01 | 319 | 7.41% | 8.36% | 0.67 | 0.89 | -19.81% | 0.33 | 0.36 | 4.59% | 3.24% | 0.26 | 9.79% | 9.04% | 6.14% |
| `iEQDGLS` | 2000-01 | 319 | 9.82% | 7.11% | 1.09 | 1.38 | -19.02% | 0.32 | 0.47 | 6.91% | 5.53% | 0.20 | 14.51% | 15.21% | 6.36% |
| `iEQDULS` | 2000-01 | 319 | 9.70% | 7.62% | 1.00 | 1.27 | -18.53% | 0.43 | 0.72 | 5.83% | 4.66% | 0.33 | 12.44% | 12.51% | 7.18% |
| `iEQS` | 2000-01 | 319 | 13.00% | 8.44% | 1.28 | 1.54 | -20.57% | 0.40 | 0.52 | 9.35% | 8.11% | 0.24 | 19.05% | 31.60% | 11.57% |
| `iEQSCON` | 2001-01 | 307 | 7.61% | 9.96% | 0.61 | 0.76 | -19.25% | 0.30 | 0.21 | 4.97% | 3.67% | 0.28 | 4.10% | -4.90% | -3.40% |
| `iEQSEUI` | 2000-01 | 319 | 12.31% | 8.96% | 1.13 | 1.37 | -25.13% | 0.35 | 0.36 | 9.20% | 7.85% | 0.17 | 11.22% | 16.17% | 5.64% |
| `iEQSFIN` | 2000-01 | 319 | 12.04% | 9.64% | 1.03 | 1.25 | -22.57% | 0.40 | 0.39 | 8.59% | 7.34% | 0.22 | 14.22% | 15.35% | 6.53% |
| `iEQSHLT` | 2000-01 | 319 | 15.33% | 11.65% | 1.13 | 1.32 | -25.95% | 0.41 | 0.29 | 11.81% | 10.58% | 0.21 | 20.77% | 49.45% | 9.26% |
| `iEQSREI` | 2010-01 | 199 | 10.01% | 8.17% | 1.02 | 1.23 | -11.45% | 0.42 | 0.54 | 3.93% | 3.00% | 0.32 | 7.49% | 11.31% | 8.05% |
| `iEQSTMT` | 2002-01 | 295 | 10.39% | 9.34% | 0.93 | 1.11 | -27.51% | 0.42 | 0.46 | 5.99% | 4.96% | 0.35 | 21.64% | 25.80% | 16.45% |
| `iEVD` | 2000-01 | 319 | 9.29% | 6.87% | 1.05 | 1.35 | -21.24% | 0.30 | 0.44 | 6.61% | 5.17% | 0.17 | 11.44% | 12.78% | 6.97% |
| `iEVDESS` | 2000-01 | 319 | 11.59% | 8.56% | 1.11 | 1.35 | -16.54% | 0.38 | 0.45 | 8.24% | 6.94% | 0.21 | 11.59% | 13.78% | 8.88% |
| `iEVDMER` | 2000-01 | 319 | 9.57% | 6.90% | 1.09 | 1.39 | -13.63% | 0.13 | 0.08 | 8.51% | 6.71% | -0.07 | 9.05% | 8.45% | 4.21% |
| `iEVDMEV` | 2000-01 | 319 | 9.22% | 7.04% | 1.02 | 1.31 | -22.28% | 0.31 | 0.44 | 6.48% | 5.06% | 0.18 | 11.94% | 13.59% | 7.35% |
| `iGBM` | 2000-01 | 319 | 7.83% | 5.89% | 0.97 | 1.33 | -9.43% | 0.01 | 0.00 | 7.87% | 5.81% | -0.12 | 7.24% | 10.16% | 4.24% |
| `iGBMCOM` | 2006-01 | 247 | 12.21% | 8.62% | 1.18 | 1.42 | -15.14% | 0.01 | 0.00 | 12.48% | 10.59% | -0.17 | 0.91% | 3.71% | -1.35% |
| `iGBMDSC` | 2001-01 | 307 | 9.63% | 5.54% | 1.37 | 1.74 | -11.62% | 0.08 | 0.04 | 8.98% | 7.19% | -0.11 | 9.07% | 10.63% | 2.74% |
| `iGBMMMA` | 2000-01 | 319 | 9.82% | 5.05% | 1.51 | 1.94 | -5.50% | -0.03 | 0.01 | 10.29% | 8.10% | -0.24 | 5.81% | 4.45% | 0.04% |
| `iGBMQNT` | 2000-01 | 319 | 6.42% | 6.95% | 0.65 | 0.92 | -10.80% | -0.04 | 0.01 | 7.01% | 4.87% | -0.14 | 5.19% | 9.57% | 5.37% |
| `iGBMRPM` | 2008-01 | 223 | 8.09% | 10.20% | 0.68 | 0.79 | -24.51% | 0.34 | 0.28 | 4.31% | 3.34% | 0.25 | 10.96% | 16.09% | 9.67% |
| `iHFC` | 2000-01 | 319 | 9.27% | 4.80% | 1.48 | 1.93 | -12.82% | 0.20 | 0.39 | 7.46% | 5.79% | 0.06 | 10.65% | 13.18% | 6.11% |
| `iHFCEW` | 2000-01 | 319 | 9.84% | 5.21% | 1.47 | 1.89 | -14.01% | 0.24 | 0.47 | 7.65% | 6.07% | 0.10 | 11.26% | 14.25% | 6.60% |
| `iMFT` | 2000-01 | 319 | 7.22% | 9.33% | 0.59 | 0.77 | -15.36% | -0.10 | 0.03 | 8.67% | 6.36% | -0.18 | 2.01% | 15.93% | 6.43% |
| `iMFTNTF` | 2000-01 | 319 | 6.54% | 7.18% | 0.65 | 0.91 | -13.30% | -0.12 | 0.07 | 8.02% | 5.69% | -0.27 | 1.21% | 9.96% | 9.87% |
| `iMFTTFO` | 2000-01 | 319 | 7.39% | 11.77% | 0.50 | 0.63 | -18.61% | -0.10 | 0.02 | 9.09% | 6.78% | -0.15 | 2.51% | 20.00% | 6.45% |
| `iMST` | 2000-01 | 319 | 9.12% | 4.20% | 1.66 | 2.17 | -15.05% | 0.13 | 0.23 | 7.90% | 6.11% | -0.04 | 10.40% | 12.04% | 5.79% |
| `iQNT` | 2003-01 | 283 | 6.21% | 4.55% | 0.96 | 1.36 | -19.18% | 0.13 | 0.18 | 4.65% | 3.08% | -0.01 | 10.84% | 8.40% | 3.60% |
| `iRVI` | 2000-01 | 319 | 8.72% | 3.39% | 1.95 | 2.57 | -11.42% | 0.10 | 0.18 | 7.83% | 5.96% | -0.07 | 9.19% | 10.26% | 4.71% |
| `iVOL` | 2000-01 | 319 | 8.50% | 5.00% | 1.29 | 1.70 | -4.64% | -0.06 | 0.04 | 9.26% | 7.04% | -0.27 | 4.11% | 6.06% | 4.17% |
| **`SP500TR`** | 2000-01 | 319 | 8.29% | 15.19% | - | - | -50.95% | 1.00 | 1.00 | - | - | - | - | - | - |

*Since inception, through 2026-07. Full precision and the trailing 36-month window are in [`index_statistics.csv`](index_statistics.csv).*

- **Sharpe** is annualised and excess of the risk-free rate (3-month U.S. T-Bill, FRED `DGS3MO`), consistent with PivotalPath's published factsheets.
- **Return/vol** is the raw ratio of annualised return to annualised volatility, with *no* risk-free subtraction. It is not a Sharpe ratio and should not be quoted as one.
- **Alpha is published twice, under two names.** `alpha_sp500_ann_*` is the intercept of a **raw**-return regression of the index on S&P 500 total return, with no risk-free subtraction anywhere. `jensens_alpha_sp500_ann_*` subtracts the risk-free rate from **both** the index and the market, per the standard Jensen definition, using the same risk-free series as `sharpe_ratio`. They are different numbers; quote the one you mean.
- Each alpha ships with the **beta** and **R²** from its own fit — `beta_sp500_*` / `r_squared_sp500_*` for the raw regression, `beta_sp500_excess_*` / `r_squared_sp500_excess_*` for the excess one. Read them together: where R² is low the index is largely unexplained by equity market moves and the alpha carries little meaning.
- **Downside capture** answers what `max_drawdown` cannot. A drawdown is the index's own worst run, whenever it happened; `downside_capture_sp500_*` is the mean index return in months the S&P **fell**, over the mean S&P return in those months — below 1.00 means the index fell less than the market did. `up`/`down` capture ship with `down_months_count_*`, because a ratio over few months is noise, and with `return_worst10_sp500_*` alongside `sp500_worst10_mean_*` — the index and the market across the same ten worst market months.
- The S&P 500 itself is in [`benchmark_reference.csv`](benchmark_reference.csv) (return, volatility, drawdown, both windows) and its calendar years are in `cy_returns.csv` under id `SP500TR`. A capture ratio is a comparison, so the thing compared against is published too.
- **Trailing periods** follow one rule, stated in every column name: periods longer than a year are **annualised** (`ann_return_2y`, `_3y`, `_5y`, `_10y`), periods of a year or less are **cumulative** (`cum_return_1y`, `cum_return_ytd`, `cum_return_mtd`). A window is published only when every month in it is present; otherwise the cell is **empty**, never zero.
- **Quarters** are published under their calendar name (`q_2026Q2`), never a relative one. A column named for a quarter means the same thing permanently; the set rolls forward as quarters complete, so read the header rather than assuming a fixed position.
- **Calendar-year returns** are in [`cy_returns.csv`](cy_returns.csv), one row per index-year. The running year carries `partial=1` and its `months` count, so it can never be read as a full year.
- Exactly two figures use **excess** returns: `sharpe_ratio` and `jensens_alpha_sp500_ann_*`, both against the same risk-free series. Everything else here — trailing periods, calendar years, capture ratios and `alpha_sp500_ann_*` — is on **raw** returns.

<!-- STATS:END -->
