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
| [`index_catalog_extended.csv`](index_catalog_extended.csv) | The remaining sub-indices - id, name and description. Returns for these are **not** published here; see the PivotalPath Index App. |
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

### `index_catalog_extended.csv`

Same three columns as `index_catalog.csv`. These are the deeper sub-indices of the published hierarchy: they are listed so the full index family is visible, but **their return series are not published in this repository**. For those, see the PivotalPath Index App.

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
| `iCRD` | 2000-01 | 318 | 8.92% | 4.87% | 1.39 | 1.83 | -17.51% | 0.17 | 0.28 | 7.40% | 5.68% | 0.00 | 8.91% | 7.29% | 3.48% |
| `iEQD` | 2000-01 | 318 | 9.82% | 7.04% | 1.10 | 1.39 | -18.50% | 0.37 | 0.65 | 6.41% | 5.13% | 0.26 | 14.33% | 16.93% | 8.78% |
| `iEQS` | 2000-01 | 318 | 13.23% | 8.40% | 1.31 | 1.58 | -20.57% | 0.40 | 0.53 | 9.56% | 8.32% | 0.23 | 21.14% | 42.02% | 16.47% |
| `iEVD` | 2000-01 | 318 | 9.37% | 6.87% | 1.06 | 1.36 | -21.24% | 0.30 | 0.44 | 6.67% | 5.24% | 0.17 | 12.54% | 15.97% | 8.17% |
| `iGBM` | 2000-01 | 318 | 7.86% | 5.90% | 0.98 | 1.33 | -9.43% | 0.01 | 0.00 | 7.91% | 5.85% | -0.12 | 7.48% | 10.64% | 4.54% |
| `iHFC` | 2000-01 | 318 | 9.35% | 4.80% | 1.49 | 1.95 | -12.82% | 0.20 | 0.39 | 7.52% | 5.87% | 0.06 | 11.43% | 15.53% | 7.30% |
| `iMFT` | 2000-01 | 318 | 7.26% | 9.35% | 0.59 | 0.78 | -15.36% | -0.10 | 0.03 | 8.73% | 6.42% | -0.18 | 2.16% | 17.09% | 7.12% |
| `iMST` | 2000-01 | 318 | 9.18% | 4.20% | 1.68 | 2.19 | -15.05% | 0.13 | 0.23 | 7.96% | 6.16% | -0.04 | 10.90% | 13.60% | 6.47% |
| `iQNT` | 2003-01 | 282 | 6.15% | 4.55% | 0.95 | 1.35 | -19.18% | 0.13 | 0.18 | 4.58% | 3.02% | -0.00 | 10.39% | 4.00% | 1.68% |
| `iVOL` | 2000-01 | 318 | 8.52% | 5.01% | 1.29 | 1.70 | -4.64% | -0.06 | 0.04 | 9.29% | 7.07% | -0.27 | 4.19% | 5.35% | 3.94% |
| **`SP500TR`** | 2000-01 | 318 | 8.32% | 15.22% | - | - | -50.95% | 1.00 | 1.00 | - | - | - | - | - | - |

*Since inception, through 2026-06. Full precision and the trailing 36-month window are in [`index_statistics.csv`](index_statistics.csv).*

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
