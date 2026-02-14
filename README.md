# Unrealized Gains Tax Simulator

Monte Carlo simulator for the impact of the proposed Dutch Box 3 unrealized gains tax on long-term portfolio wealth. Runs entirely client-side in the browser.

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
| Unrealized Gains Tax Rate | Annual tax on unrealized gains (%). Set 0 for no-tax baseline |
| Loss carry-forward | Offset future gains with prior-year losses |
| Tax-free allowance | Exempt threshold per person (€57,000 for NL 2025) |
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

Returns are modelled as Geometric Brownian Motion with monthly steps. Tax is assessed annually: gains (including unrealized) are taxed and paid from the portfolio.

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