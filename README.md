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
> format** (one row per index-month). History runs from **1998-01** for nine
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

| Index | From | Months | Ann. return | Ann. vol | Sharpe | Return/vol | Max DD | Beta (S&P) | R² |
|---|---|---|---|---|---|---|---|---|---|
| `iCRD` | 1998-01 | 342 | 8.96% | 4.94% | 1.33 | 1.81 | -17.51% | 0.17 | 0.29 |
| `iEQD` | 1998-01 | 342 | 11.21% | 7.30% | 1.21 | 1.54 | -18.50% | 0.37 | 0.60 |
| `iEQS` | 1998-01 | 342 | 12.11% | 9.37% | 1.05 | 1.29 | -26.11% | 0.43 | 0.49 |
| `iEVD` | 1998-01 | 342 | 9.84% | 7.13% | 1.06 | 1.38 | -21.24% | 0.31 | 0.44 |
| `iGBM` | 1998-01 | 342 | 8.55% | 6.07% | 1.03 | 1.41 | -9.43% | 0.01 | 0.00 |
| `iHFC` | 1998-01 | 342 | 9.71% | 4.81% | 1.52 | 2.02 | -12.82% | 0.19 | 0.37 |
| `iMFT` | 1998-01 | 342 | 7.55% | 9.76% | 0.58 | 0.77 | -15.36% | -0.12 | 0.03 |
| `iMST` | 1998-01 | 342 | 9.25% | 4.57% | 1.51 | 2.02 | -15.05% | 0.13 | 0.19 |
| `iQNT` | 2003-01 | 282 | 6.15% | 4.55% | 0.95 | 1.35 | -19.18% | 0.13 | 0.18 |
| `iVOL` | 1998-01 | 342 | 9.62% | 6.34% | 1.16 | 1.52 | -8.41% | -0.05 | 0.02 |

*Since inception, through 2026-06. Full precision and the trailing 36-month window are in [`index_statistics.csv`](index_statistics.csv).*

- **Sharpe** is annualised and excess of the risk-free rate (3-month U.S. T-Bill, FRED `DGS3MO`), consistent with PivotalPath's published factsheets.
- **Return/vol** is the raw ratio of annualised return to annualised volatility, with *no* risk-free subtraction. It is not a Sharpe ratio and should not be quoted as one.
- **Beta**, **R²**, correlation and alpha come from a regression of **raw** index returns on **raw** S&P 500 **total return** — no risk-free subtraction, so `alpha_sp500_ann` is the annualised regression intercept, not Jensen's alpha. Read beta and R² together: where R² is low the index is largely unexplained by equity market moves, and the corresponding alpha carries little meaning.

<!-- STATS:END -->
