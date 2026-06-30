# Skewness as a Cross-Sectional Signal: Testing a Free-Theory Null Against Observed Non-Gaussianity

## Motivation

Standard factor models describe returns through mean and variance, implicitly treating returns as approximately Gaussian. This project asks a narrower and more careful question than "is skewness priced": **is the non-Gaussianity observed in stock returns a fundamental feature of the return-generating process, or is it an artifact of time-varying volatility?**

A purely Gaussian process with time-varying volatility (a GARCH process) can generate nonzero unconditional skewness in finite samples with no genuine asymmetric mechanism anywhere in the model. This project measures how much of the observed skewness in US equities can be explained by this mechanism alone, and what is left over once it is subtracted out.

This framing borrows language from quantum field theory as a motivating analogy (a free theory with a time-varying mass term vs. loop-induced corrections to an n-point function vs. genuine interaction vertices), but the methodology itself is standard econometrics: GARCH modeling, Monte Carlo simulation, and cross-sectional asset pricing tests. See `WRITEUP.md` for the full treatment, including the analogy and its limits.

## Repository structure

```
skewness-project/
├── analysis.ipynb        # main notebook: all analysis, in sequence
├── results/              # output plots and cached parquet files
├── README.md
└── WRITEUP.md
```

## How to run

1. Install dependencies: `pip install yfinance pandas numpy scipy arch matplotlib`
2. Open `analysis.ipynb` and run cells in order. The config block at the top sets the date range, rolling window, and universe filters.
3. Data is cached to `results/` after the first run.

## Data

Daily stock returns from 2005–present, sourced via `yfinance`. Delisted stocks are explicitly included to mitigate survivorship bias.

**Caveat:** `yfinance` is not a research-grade data vendor. Pre-2007 coverage of delisted names is thinner than later years, and a small number of extreme return observations (likely data artifacts around delisting events) required explicit winsorization. See `WRITEUP.md` for details.

## Headline results

- Raw skewness and coskewness both have statistically significant nonzero amplitude in the cross-section (t-stats of +16.6 and -7.96), but neither predicts future returns (Fama-MacBeth t-stats of -0.87 and -1.07; portfolio Sharpe ratios of -0.08 and +0.26).
- The cross-sectional correlation between raw skewness and coskewness behaves as a leading indicator of systemic stress, peaking in the run-up to the 2008, 2011, and 2020 crises.
- A GARCH(1,1)-Gaussian null model — pure volatility clustering with no asymmetric mechanism — explains essentially none of the observed cross-sectional skewness amplitude.
- The residual skewness (what GARCH cannot explain) is large and statistically significant in every sub-period tested, and traces a slow, economically interpretable arc: positive and large before 2008, decaying through the financial crisis, flipping negative from the 2011 Eurozone crisis onward, and reaching its most negative point during COVID.

## Known limitations

- Universe selection/filter could be done dynamically by considering: price limits by market cap, average daily dollar volume over the trailing days, applied at each rebalance date.
- The GARCH(1,1)-Gaussian subtraction is one specific choice of free-theory benchmark, not the only possible one. The residual is defined relative to this choice and would differ under other model choices such as EGARCH.
- A near-zero residual would not imply the absence of interactions. Cancellation between contributions from different sources is possible, and is not distinguishable from "no interactions" using this method alone.
- Coskewness decomposition against a GARCH null was attempted but abandoned. The simulation design could not produce a non-degenerate result, and a proper null for coskewness (e.g., a beta-implied benchmark) is left as future work.

Full methodology, results, and discussion: see `WRITEUP.md`.
