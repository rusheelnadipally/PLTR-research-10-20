# Lab 14 — Palantir Project: Portfolio Baselines

## D — Question

What does each simple portfolio earn and risk, and does any of them meet the Meridian mandate?

## V — Known-answer results

The calculations in `portfolio.py` produce:

| Portfolio | Weights: bills / 10-year / IG / equity | Return | Volatility | Reserve-eligible | Meets 6.5%? |
|---|---:|---:|---:|---:|---|
| Benchmark | 0.0 / 0.0 / 40.0 / 60.0 | 6.80% | 10.46% | 0.0% | yes |
| Equal weight | 25.0 / 25.0 / 25.0 / 25.0 | 5.20% | 5.21% | 50.0% | no |
| Inverse volatility | 84.3 / 6.0 / 7.0 / 2.6 | 3.77% | 1.05% | 90.3% | no |

The benchmark meets the return objective but fails Meridian's 3.0% reserve rule because it has no bills or 10-year Treasury notes. The equal-weight and inverse-volatility portfolios satisfy the reserve rule but do not meet the 6.5% return objective. Therefore, none of the three baselines satisfies both the objective and all checkable mandate rules.

## Validation

- Changing both IG-bond/equity correlation cells from 0.25 to 0.50 raises benchmark volatility from 10.46% to 11.00%, proving the output is computed from the inputs.
- A correlation of 1.3 is rejected with a clear input error.
- Adding the 40% IG-bond / 60% equity benchmark to `mandate.py` as candidate E makes it fail the reserve rule at 0.0%.

## Diversification

Diversification combines assets whose returns do not move perfectly together. Portfolio variance includes every pair of assets as `weight_i × weight_j × volatility_i × volatility_j × correlation_i,j`. When correlation is below +1, one asset's movement does not fully reinforce the other asset's movement, so the portfolio can have less risk than a simple weighted average of the individual risks.

For the benchmark:

`variance = (0.40² × 0.06²) + (0.60² × 0.16²) + (2 × 0.40 × 0.60 × 0.06 × 0.16 × 0.25)`

`variance = 0.010944`, so `volatility = sqrt(0.010944) = 10.46%`.

Inverse volatility piles into Treasury bills because a 0.5% bill volatility produces an inverse-volatility score of 200, far larger than the scores for the 7%, 6%, and 16% volatility sleeves. The method controls risk mechanically and ignores the 6.5% return objective, so it assigns 84.3% to bills and earns only 3.77%.

I would show the CIO the benchmark first because it meets the stated return objective and corresponds to the client's chosen 60/40 benchmark. I would immediately flag that it fails the reserve rule, which demonstrates that the client's own benchmark is inconsistent with the client's liquidity mandate and needs revision rather than blind implementation.

## Palantir project connection

My Palantir analysis reached a watch/defer conclusion: the company can be strong while the stock can still be too expensive under the cash-flow assumptions I can defend. Lab 14 reinforces the same separation between an attractive attribute and a suitable portfolio. A portfolio may meet a return target yet remain unsuitable because it violates liquidity or reserve constraints, just as a high-quality company may remain an unsuitable investment at an unsupported price.

For Project 2, I frame the client as a conservative long-horizon investor who is interested in growth companies such as Palantir but needs a diversified, liquid portfolio rather than a single-stock position.

### Project 2 mandate

- **Objective:** target CPI + 4% over rolling five-year periods.
- **Hard constraints:** long-only; no borrowing, short sales, or derivatives; at least 3% in reserve-eligible assets; at least 90% redeemable within five business days; no individual company above 8%; annual turnover at most 25% without approval.
- **Preference:** broad diversification across cash, government bonds, investment-grade fixed income, and global equities. Palantir may appear only through a diversified equity fund unless the client separately approves an individual-stock sleeve within the 8% issuer limit.
- **Unverifiable rule:** “no calendar-year loss greater than 10%” cannot be tested until the client specifies the risk measure and breach response.
- **One question before optimization:** should the portfolio minimize volatility, maximize expected return subject to the rules, or minimize the risk of failing the CPI + 4% objective? The answer changes the optimized weights.

### Fund universe

| Ticker | Fund | Role |
|---|---|---|
| SHY | iShares 1-3 Year Treasury Bond ETF | Short-duration government reserve proxy |
| IEF | iShares 7-10 Year Treasury Bond ETF | Government bonds |
| LQD | iShares iBoxx $ Investment Grade Corporate Bond ETF | Investment-grade fixed income |
| SPY | SPDR S&P 500 ETF Trust | Broad equity |

These funds create the diversified baseline around the Palantir thesis; they are practice inputs, not recommendations. The accompanying `prices.csv` contains 61 monthly adjusted-close observations from August 2021 through August 2026, with the oldest month first. The data comes from the [marketdata adjusted-close mirror](https://github.com/manisahni/marketdata), downloaded October 20, 2026. Its source file identifies the figures as dividend- and split-adjusted closes generated with `yfinance` auto-adjustment. SHY is a short-duration Treasury proxy rather than a money-market fund, so its price history can show return variation.

## Reflection

A portfolio can pass every coded rule and still be wrong because suitability also depends on an objective that the client has not selected, the reliability of the return estimates, tolerance for interim loss, taxes, fees, and qualitative preferences. Candidate D in `mandate.py` proves the point: its 95% global-equity allocation passes the coded rules but could reasonably be rejected because it departs sharply from the client's 60/40 benchmark and may be inconsistent with the unresolved loss statement.

The baseline I would not choose automatically is inverse volatility. Its low measured risk looks attractive, but its 84.3% bill allocation makes the 6.5% objective unattainable under the supplied assumptions. The same discipline applies to Palantir: no single metric—growth, volatility, or valuation—decides suitability by itself.

## Run commands

```text
python3 portfolio.py
python3 mandate.py
```

## Submission links

Add the GitHub URLs after uploading:

- `portfolio.py`: GITHUB_URL_NEEDED
- `mandate.py`: GITHUB_URL_NEEDED
- `prices.csv`: GITHUB_URL_NEEDED
- `Lab14_Palantir_portfolio_baselines.md`: GITHUB_URL_NEEDED
