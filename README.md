# Unrealized Gains Tax Simulator

Monte Carlo simulator comparing the impact of the proposed Dutch Box 3 unrealized gains tax vs. the German Abgeltungssteuer on long-term portfolio wealth. Runs entirely client-side in the browser.

## Usage

Serve the directory with any static HTTP server and open `index.html`:

```sh
python3 -m http.server 8000
```

Then visit `http://localhost:8000`.

## Parameters

| Input | Description |
|---|---|
| Starting Capital | Initial portfolio value (€) |
| Simulation Duration | Time horizon in years |
| Monthly Savings / Withdrawal | Positive = deposit, negative = withdrawal |
| NL Unrealized Gains Tax Rate | Annual tax on unrealized gains (%). Set 0 for no-tax baseline |
| Loss carry-forward | Offset future gains with prior-year losses |
| Tax-free allowance | NL exempt threshold per person (€57,000 for 2025) |
| DE comparison toggle | Show German Abgeltungssteuer paths side-by-side |
| Index Model | AEX, MSCI World, DAX, Bitcoin, or custom return/volatility |
| Number of Simulations | Monte Carlo paths (1–500) |
| Y-Axis Scale | Smart (clip outliers), Show all, or Logarithmic |

## Market Models

| Model | Annual Return | Annual Volatility | Source |
|---|---|---|---|
| AEX | 8.5% | 18% | Euronext factsheet, curvo.eu (since 1983) |
| MSCI World | 8.5% | 14.5% | MSCI factsheet (since 1988) |
| DAX | 7% | 19% | curvo.eu (since 1970) |
| Bitcoin | 30% | 60% | curvo.eu, recent 5-8y data (use with caution) |

Returns are modelled as Geometric Brownian Motion with monthly steps.

## Tax Models

### 🇳🇱 NL Box 3 — Unrealized Gains Tax (proposed)

Annual tax on the full change in portfolio value (realized + unrealized). Tax is paid from the portfolio each year, reducing the invested amount. Supports loss carry-forward and a tax-free allowance.

### 🇩🇪 DE Abgeltungssteuer — Realized Gains Tax

German capital gains tax on realized gains only. Configurable parameters:

| Parameter | Default | Description |
|---|---|---|
| Kirchensteuer | None | None (26.375% total), 8% BW/BY (27.82%), or 9% other states (28.00%) |
| Basiszins | 2.53% | Bundesbank reference rate for the annual Vorabpauschale (2025 value) |
| Sparer-Pauschbetrag | €1,000 | Annual tax-free allowance on capital income per person |
| Teilfreistellung | 30% | Tax exemption for equity funds (Aktienfonds ≥51% equities) |

The effective tax rate is 25% Abgeltungssteuer + 5.5% Solidaritätszuschlag + optional Kirchensteuer. Withdrawals trigger proportional gain realization. The chart shows portfolio values **net of deferred tax liability** for fair comparison with the NL model.

## Y-Axis Scaling

Highly volatile assets (e.g. Bitcoin) can produce outlier paths that compress the chart. Three scale modes are available:

| Mode | Behaviour |
|---|---|
| **Smart** (default) | Caps y-axis at ~1.3× the P90 envelope. Outliers run off the top, but median and bulk of paths stay readable. |
| **Show all** | Plain linear scale showing every path. Can be hard to read with extreme spread. |
| **Logarithmic** | Log scale — shows all paths, compresses large ranges. Good for multi-order-of-magnitude spreads. |

## Disclaimer

This is a simplified simulation tool, not financial advice. Real tax rules have additional nuances. Parameters are based on historical data and do not predict future performance.

## License

[WTFPL](LICENSE)