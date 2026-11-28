# PivotalPath Public Indices

> Institutional-quality hedge fund index returns — monthly, net of fees, free and machine-readable.

[PivotalPath](https://www.pivotalpath.com) is a hedge fund research and
due-diligence firm. Since 2013 we've built unbiased, institutionally-relevant
data on the hedge fund industry on behalf of allocators and managers. This
repository is the **public expression of that research**: monthly returns and
statistics for nine headline hedge fund indices, free for non-commercial use with
attribution ([CC BY-NC 4.0](LICENSE)).

The indices track funds reporting **monthly performance, net of all fees, in
USD**, with a minimum track record of 18 months and a minimum AUM of $50mm.
Constituents are fixed at the end of each calendar year for the following year;
indices are rebalanced monthly.

## What's in this repo

| File | What it is |
| --- | --- |
| [`index_catalog.csv`](index_catalog.csv) | The nine indices — id, display name, and a full description. |
| [`index_return.csv`](index_return.csv) | Monthly returns of the nine indices, in long (tidy) format. |
| [`index_statistics.csv`](index_statistics.csv) | Summary statistics per index, derived from `index_return.csv`. |
| [`cy_returns.csv`](cy_returns.csv) | Calendar-year returns, one row per index-year, with the S&P 500 (`SP500TR`). |
| [`quarterly_returns.csv`](quarterly_returns.csv) | Quarterly returns, one row per index-quarter, with the S&P 500 (`SP500TR`). |
| [`benchmark_reference.csv`](benchmark_reference.csv) | The S&P 500's own return, volatility and drawdown over the same windows. |
| [`composite_rolling_correlation.csv`](composite_rolling_correlation.csv) | 36-month rolling correlation of the Composite (`iHFC`) to the S&P 500. |
| [`index_catalog_extended.csv`](index_catalog_extended.csv) | Reference only: id, name and description of every PivotalPath index, including those whose data is not published here. |
| [`metadata.json`](metadata.json) | `as_of`, `updated_at`, refresh cadence, expected reporting lag and `public_ids`. |

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
> format** (one row per index-month). History runs from **2000-01** for all nine
> indices. Per-index coverage is in `index_statistics.csv` (`coverage_start`,
> `months`).

## Indices

The nine indices published here (`public_ids` in `metadata.json`):

| id | Index | Focus |
| --- | --- | --- |
| `iHFC` | PivotalPath Composite | All strategies and geographies (the headline index). |
| `iCRD` | PivotalPath Credit | Strategies trading primarily in credit markets. |
| `iEQD` | PivotalPath Equity Diversified | Diversified equity long/short across geographies. |
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

Licensed under [Creative Commons Attribution-NonCommercial 4.0 International (CC BY-NC 4.0)](LICENSE)
— free to use, share, and adapt for non-commercial purposes, as long as you give
appropriate credit to PivotalPath.

Commercial use, including redistribution, resale, or incorporation into a paid
product or service, requires a license from PivotalPath. Contact
inquiry@pivotalpath.com.

## Index statistics

<!-- STATS:START -->

| Index | From | Months | Ann. return | Ann. vol | Sharpe | Return/vol | Max DD | Beta (S&P) | R² | α raw | Jensen's α | Down capt. | 3Y ann. | 1Y cum. | YTD cum. |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| `iCRD` | 2000-01 | 321 | 8.81% | 4.86% | 1.36 | 1.81 | -17.51% | 0.17 | 0.27 | 7.28% | 5.55% | 0.00 | 7.93% | 4.12% | 2.92% |
| `iEQD` | 2000-01 | 321 | 9.64% | 7.03% | 1.07 | 1.37 | -18.50% | 0.37 | 0.65 | 6.23% | 4.94% | 0.27 | 13.47% | 9.23% | 6.57% |
| `iEQS` | 2000-01 | 321 | 12.91% | 8.44% | 1.27 | 1.53 | -20.57% | 0.40 | 0.52 | 9.25% | 8.00% | 0.24 | 20.04% | 21.57% | 11.53% |
| `iEVD` | 2000-01 | 321 | 9.18% | 6.86% | 1.04 | 1.34 | -21.24% | 0.30 | 0.44 | 6.49% | 5.04% | 0.17 | 11.14% | 8.12% | 5.65% |
| `iGBM` | 2000-01 | 321 | 7.88% | 5.88% | 0.98 | 1.34 | -9.43% | 0.01 | 0.00 | 7.92% | 5.84% | -0.12 | 7.36% | 9.51% | 6.89% |
| `iHFC` | 2000-01 | 321 | 9.23% | 4.79% | 1.47 | 1.93 | -12.82% | 0.20 | 0.39 | 7.41% | 5.74% | 0.06 | 10.78% | 9.84% | 6.66% |
| `iMFT` | 2000-01 | 321 | 7.43% | 9.34% | 0.61 | 0.80 | -15.36% | -0.10 | 0.03 | 8.89% | 6.56% | -0.19 | 3.44% | 18.04% | 13.60% |
| `iMST` | 2000-01 | 321 | 9.07% | 4.19% | 1.65 | 2.17 | -15.05% | 0.13 | 0.23 | 7.85% | 6.04% | -0.04 | 10.10% | 9.38% | 6.09% |
| `iVOL` | 2000-01 | 321 | 8.44% | 4.99% | 1.28 | 1.69 | -4.64% | -0.06 | 0.04 | 9.21% | 6.97% | -0.27 | 3.45% | 4.34% | 4.01% |
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
