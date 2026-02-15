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

The Netherlands is reforming its Box 3 wealth tax ("Wet werkelijk rendement") to tax **actual returns including unrealized gains** annually, replacing the current fictitious-return system. The bill targets a 2028 start date.

How it works in the simulation:

1. At the end of each year, the **total change in portfolio value** is computed (price gains + contributions − withdrawals). This includes unrealized gains — you don't need to sell anything for the tax to apply.
2. The gain is taxed at the configured rate (default 36%, the current Box 3 rate). The tax is paid from the portfolio, shrinking the invested capital.
3. **Tax-free allowance (vrijstelling):** The first €57,000 per person (2025) of portfolio value is exempt. Only the fraction of the portfolio above this threshold is subject to tax. Set to 0 to ignore, or double it for fiscal partners.
4. **Loss carry-forward (verliesverrekening):** If enabled, years with negative returns create a loss balance that offsets gains in future years before tax is applied. This prevents paying tax immediately after recovering from a dip. The proposed law includes this mechanism.

The key impact: because tax is levied every year on paper gains, the portfolio loses compounding power. Even in years where you don't sell anything, capital is drained to pay the tax bill.

### 🇩🇪 DE Abgeltungssteuer — Realized Gains Tax

Germany taxes capital gains only when **realized** (i.e. when you actually sell). For buy-and-hold ETF investors, the main annual tax event is the small Vorabpauschale. This model is included for comparison to show how a realized-gains regime differs in long-term wealth impact.

How it works in the simulation:

1. **Abgeltungssteuer:** Flat 25% on capital gains + 5.5% Solidaritätszuschlag on top = **26.375%** effective rate. With Kirchensteuer: 27.82% (8%, BW/BY) or ~28% (9%, other states).
2. **Teilfreistellung:** For equity funds (≥51% equities), 30% of gains are tax-exempt. Only 70% of any gain is taxable.
3. **Vorabpauschale:** A small annual advance tax on ETFs. Calculated as: fund value at start of year × Basiszins × 0.7, capped at the actual year's gain. If the fund lost value, it's zero. This prevents indefinite tax deferral but is much smaller than a full unrealized gains tax.
4. **Sparer-Pauschbetrag:** €1,000 per person (€2,000 joint) annual tax-free allowance on all capital income. Applied before the Vorabpauschale and realized gains.
5. **On withdrawal/sale:** The proportional share of unrealized gains is realized and taxed (after Teilfreistellung and Pauschbetrag). Previously paid Vorabpauschale is credited against the cost basis to avoid double taxation.

| Parameter | Default | Description |
|---|---|---|
| Kirchensteuer | None | None (26.375%), 8% BW/BY (27.82%), or 9% other states (~28%) |
| Basiszins | 2.53% | Bundesbank reference rate for Vorabpauschale (2025 value) |
| Sparer-Pauschbetrag | €1,000 | Annual tax-free allowance per person |
| Teilfreistellung | 30% | Equity fund exemption |

To make the comparison fair, the chart shows the DE portfolio **net of deferred tax liability** — i.e. what you'd have left if you liquidated everything at that point. Without this adjustment, the DE line would look artificially better simply because tax hasn't been collected yet.

## Reading the Chart

Up to three groups of simulation paths are shown, each with faded individual Monte Carlo paths and a highlighted median line:

| Color | Line style | Meaning |
|---|---|---|
| 🔵 Blue (solid) | Solid median, faded paths | **NL Box 3** — portfolio after annual unrealized gains tax |
| 🟠 Orange (short dash) | Dashed median, faded paths | **DE Abgeltungssteuer** — portfolio net of deferred tax liability |
| 🟢 Teal (long dash) | Dashed median, faded paths | **No tax** — baseline without any taxation |

The shaded blue band behind the NL paths marks the P10–P90 range (middle 80% of outcomes). Paths outside this range are visible as faded lines but may be clipped by the Smart y-axis scaling.

Statistics cards below the chart show median final values, total tax paid, tax drag, and a direct NL-vs-DE comparison. The three-column comparison table at the bottom gives P10/median/P90/mean for each regime side by side.

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