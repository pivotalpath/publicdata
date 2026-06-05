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
> format** (one row per index-month). History runs from **1998-01** to the most
> recent reported month.

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

Free to use with attribution. See [`LICENSE`](LICENSE).
<!-- TODO: confirm the license you want and add a LICENSE file (e.g. CC BY 4.0). -->
