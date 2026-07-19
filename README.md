# Factor-Based Return Attribution using Fama-French Multi-Factor Regression on NSE Stocks

Attributing the realized returns of eight NSE large-cap stocks to systematic risk factors — market, size, value, and momentum — and testing how much of that attribution survives real econometric scrutiny, not just a single regression run.

## Motivation

A single CAPM beta is the starting point for most equity risk conversations, but it collapses years of changing market conditions into one static number and rarely gets checked against the assumptions OLS actually requires. This project builds up from CAPM to a Fama-French three-factor model to a Carhart four-factor model, runs the standard battery of regression diagnostics on each, and tracks how factor exposures actually moved over time using rolling-window regression — framed throughout around the kind of questions a market risk, model validation, or quant research desk would ask.

## Universe and Period

**Stocks:** Reliance, HDFC Bank, Infosys, Sun Pharma, Tata Steel, Maruti Suzuki, ITC, Bharti Airtel
**Period:** February 2015 – December 2025 (monthly)
**Frequency:** Monthly, chosen to match standard Fama-French convention and the coverage of the factor data source below

## Data Sources

| Data | Source |
|---|---|
| Stock prices | Yahoo Finance via `yfinance` |
| Market premium, SMB, HML, WML, Risk-Free Rate | [Fama-French and Momentum Factor Library, IIM Ahmedabad](https://faculty.iima.ac.in/iffm/Indian-Fama-French-Momentum/) |

Stock-level SMB and HML factors require point-in-time market capitalization and book-to-market data across a broad universe of stocks — not something free tools can reconstruct reliably without introducing look-ahead bias. Instead, all four factors (and the risk-free rate) are sourced from the Fama-French and Momentum factor library maintained by IIM Ahmedabad, built on CMIE Prowess data with survivorship-bias correction. This is the standard citation for Fama-French-style research on Indian equities.

> **Citation:** Agarwalla, S. K., Jacob, J., and Varma, J. R. (2013), *Four factor model in Indian equities market*, Working Paper W.P. No. 2013-09-05, Indian Institute of Management, Ahmedabad.

## Why Carhart Instead of Fama-French Five-Factor

The original scope for the model comparison phase targeted a Fama-French five-factor model (adding profitability and investment factors, RMW and CMA). However, unlike Market/SMB/HML/WML, RMW and CMA don't have a maintained, citable public data source for Indian equities — building them from scratch would reintroduce the same point-in-time data-quality risk that made a self-built SMB/HML approach unattractive in the first place. The comparison was scoped to a **Carhart four-factor model** instead, adding momentum (WML) — a well-established academic model (Carhart, 1997) already available from the same trusted data source, with no new data risk introduced.

## Methodology

1. **Data Collection and Preparation** — Download daily NSE prices, validate for missing data, resample to monthly, compute simple returns.
2. **Factor Construction** — Load and clean the IIMA factor library, align to the study window, compute stock-level excess returns.
3. **CAPM Baseline Regression** — Single-factor market model for all eight stocks.
4. **Fama-French 3-Factor Model** — Adds SMB and HML to CAPM.
5. **Regression Assumption Testing** — VIF (multicollinearity), White's test (heteroskedasticity), Durbin-Watson (autocorrelation), Jarque-Bera (residual normality), and Newey-West HAC standard errors.
6. **Rolling Window Regression** — 36-month rolling FF3 regressions to track factor exposure drift over time.
7. **CAPM vs FF3 vs Carhart 4-Factor Comparison** — Side-by-side R², adjusted R², alpha, and nested F-tests.
8. **Final Dashboard** — Six-panel consolidated summary of the project's key visuals.

## Key Findings

- **No single model fits every stock.** FF3 meaningfully improved on CAPM for 5 of 8 stocks; Carhart picked up almost exactly where FF3 left off, improving the remaining names. Only **Tata Steel** showed significant exposure to size, value, *and* momentum simultaneously.
- **Heteroskedasticity, not autocorrelation, is this portfolio's main assumption violation.** 5 of 8 stocks failed White's test; all 8 passed Durbin-Watson. Newey-West correction confirmed every significant factor finding held up under HAC standard errors — no significance conclusions flipped.
- **Static betas can be misleading.** Reliance's market beta fell from ~2.0 (2018) to under 1.0 (2022+), tracking its real diversification from oil/petrochemicals into telecom and retail. Maruti Suzuki's rolling beta ranged from -0.03 to 1.73 over the same eight years.
- **Only Sun Pharma and Bharti Airtel** passed all three OLS diagnostic tests cleanly under the FF3 specification.

## Repository Structure

```
├── Factor_based_return.ipynb   # Main analysis notebook (all 8 phases)
├── 2025-12_FourFactors_and_Market_Returns_Monthly_SurvivorshipBiasAdjusted.csv  # IIMA factor data
└── README.md
```

## How to Run

1. Clone the repository and install dependencies:
   ```
   pip install yfinance pandas numpy matplotlib seaborn statsmodels scipy
   ```
2. Download the latest Fama-French and Momentum factor file (Monthly, Survivorship-Bias Adjusted) from the [IIMA data library](https://faculty.iima.ac.in/iffm/Indian-Fama-French-Momentum/) and place it in the repository root.
3. Open `Factor_based_return.ipynb` and run all cells top to bottom.

## Limitations

- Factor coverage is bounded by the IIMA library's latest release; the study window is fixed at whatever period is currently available (Feb 2015 – Dec 2025 as of this release).
- Momentum (WML) substitutes for the profitability and investment factors (RMW, CMA) used in a true Fama-French five-factor model, for the data-availability reasons noted above.
- Eight stocks is a small, large-cap-only cross-section — findings describe this specific portfolio, not the broader Indian market.

## Author

Sohom Halder —  MSc Statistics, IIT Kanpur
[LinkedIn](https://www.linkedin.com/in/sohom-halder-6200a8406/) · [GitHub](https://github.com/sohom21-bit) · [Email](mailto:haldersohom21@gmail.com)
