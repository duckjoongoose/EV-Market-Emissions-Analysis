# EV Market & Emissions Analysis

How do U.S. gasoline prices affect electric and hybrid vehicle adoption, and has that shift shown up in national vehicle emissions? This project analyzes 25 years of federal data (2000–2024) and builds regression models to forecast EV sales through 2030.

**Notebook:** [`ev_market_analysis.ipynb`](EV%20Market%20%26%20Emissions%20Analysis%20%E2%80%93%20Data%20Science%20Final%20Project/ev_market_analysis.ipynb)

---

## Data

Three federal datasets, each downloaded from the source and cleaned manually. Both the original and cleaned versions are included in `data/`.

| Dataset | Source | Contents |
|---|---|---|
| EV & hybrid sales | Bureau of Transportation Statistics (via Argonne National Laboratory) | Annual HEV, PHEV, BEV, and fuel-cell sales, 1999–2024 |
| Gasoline prices | Dept. of Energy, Alternative Fuels Data Center | Monthly average U.S. retail fuel prices, 2000–2025 |
| Vehicle emissions | Bureau of Transportation Statistics (EPA MOVES model) | Annual CO₂ and NOₓ emission rates, grams per mile |

I limited the scope to the United States. International datasets use different reporting standards and policy environments, which would have added variables the analysis couldn't control for.

## Method

**Cleaning and merging.** The three datasets arrived in three incompatible shapes. Sales and emissions came in wide format with years as columns, so I reshaped both to long format and pivoted them into a tidy yearly structure. Gasoline prices were reported by date, so I parsed the date column into datetime objects, extracted the year, and aggregated to annual averages. All three were then merged on year into a single dataset.

**Analysis.** I compared gas prices against EV/hybrid sales, and sales against CO₂ and NOₓ emission rates, using dual-axis charts to handle the difference in scale between fuel prices (roughly $1–4) and sales volume (up to 2.6 million units).

**Modeling.** I used linear regression with lag features — predicting each year's sales from the prior year's sales and gas price — on the reasoning that EV adoption carries momentum (charging infrastructure, dealer inventory, word of mouth) and that consumers respond to fuel costs with a delay.

## Findings

**Gas prices and EV adoption move together.** Sales rose alongside fuel price increases, most visibly between 2020 and 2022. The fitted model put the relationship at roughly **94,500 additional sales per $1 increase** in the prior year's gas price.

**Emissions declined as adoption grew.** Between 2020 and 2024, CO₂ fell about 20 g/mile and NOₓ about 0.08 g/mile while EV/hybrid sales climbed.

**Training window changed the forecast dramatically.** Trained on 2000–2024, the model projected a slight decline to ~1.31M by 2030. Trained on 2020–2024 only, it projected ~1.97M — a difference of over 660,000 vehicles.

**The short-window model was overfit.** With only four training observations after lagging, the 2023→2024 sales drop dominated the fit and produced a negative coefficient on prior-year sales — implying high sales one year cause low sales the next, which makes no economic sense. This is a clear case of a model memorizing noise rather than learning a pattern.

**Fuel price assumptions dominate the forecast.** Holding the model fixed and varying only the assumed future gas price:

| Gas price | 2030 projection |
|---|---|
| $1.66 | ~693,000 |
| $3.66 (baseline) | ~1.31M |
| $5.66 | ~1.92M |

A $4 swing moves the forecast by **178%**.

## Limitations

- Correlation, not causation. Federal incentives, new model releases, and charging infrastructure all expanded over the same period and are not controlled for.
- No holdout set or error metrics. Models were compared on coefficient stability and reasoning rather than measured accuracy — a next step would be a train/test split with RMSE.
- Scenario forecasts assume a constant gas price for six years, which is unrealistic.
- The models cannot account for policy changes, recessions, or other external shocks.

## Tools

Python · pandas · NumPy · scikit-learn · matplotlib · Jupyter

---

Built as a final project for Data 202 at Calvin University.
