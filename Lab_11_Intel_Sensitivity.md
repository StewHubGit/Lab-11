# Lab 11 - Intel one-at-a-time sensitivity

Run command: `python .\intel_oat_sensitivity.py`  
Base inputs: `outputs/intel_proforma/base_inputs_2026-09-29.json`  
Full input snapshots, linked statement rows, and valuation components: `outputs/intel_proforma/Intel_OAT_run_details.json`

Units: EBIT and free cash flow to equity (FCFE) in USD billions; value per diluted share in USD. Forecast years: 2027-2031. The lower and higher paths are the existing Lab 10 judgment ranges, 2 percentage points below and above base in each forecast year.

## Actual independent inputs

| Driver and case | 2027 | 2028 | 2029 | 2030 | 2031 |
|---|---:|---:|---:|---:|---:|
| Revenue growth - lower | 10% | 8% | 6% | 4% | 2% |
| Revenue growth - base | 12% | 10% | 8% | 6% | 4% |
| Revenue growth - higher | 14% | 12% | 10% | 8% | 6% |
| EBIT margin - lower | 10% | 12.5% | 14.5% | 16% | 17% |
| EBIT margin - base | 12% | 14.5% | 16.5% | 18% | 19% |
| EBIT margin - higher | 14% | 16.5% | 18.5% | 20% | 21% |

## One-at-a-time outputs and signed changes from each driver's base

| Driver | Case | 2031 EBIT ($bn) | Change in EBIT ($bn) | 2031 FCFE ($bn) | Change in FCFE ($bn) | Value/share ($) | Change in value/share ($) | Status |
|---|---|---:|---:|---:|---:|---:|---:|---|
| Revenue growth | Lower | 15.863 | -1.555 | 10.383 | -1.303 | 21.48 | -2.20 | PASS |
| Revenue growth | Base | 17.418 | +0.000 | 11.686 | +0.000 | 23.68 | +0.00 | PASS |
| Revenue growth | Higher | 19.093 | +1.675 | 13.088 | +1.402 | 26.04 | +2.36 | PASS |
| EBIT margin | Lower | 15.585 | -1.834 | 10.127 | -1.558 | 20.58 | -3.10 | PASS |
| EBIT margin | Base | 17.418 | +0.000 | 11.686 | +0.000 | 23.68 | +0.00 | PASS |
| EBIT margin | Higher | 19.252 | +1.834 | 13.244 | +1.558 | 26.78 | +3.10 | PASS |

## Output spans across valid lower, base, and higher runs

| Driver | EBIT span ($bn) | FCFE span ($bn) | Value/share span ($) |
|---|---:|---:|---:|
| Revenue growth | 3.230 | 2.705 | 4.56 |
| EBIT margin | 3.667 | 3.117 | 6.20 |

Across these specified lower/base/higher paths, EBIT margin is the larger driver on all three measured outputs. Its value-per-share span is $6.20, versus $4.56 for revenue growth. This comparison applies to the tested ranges.

## Locked prediction and reconciliation

The prediction in `outputs/intel_proforma/Intel_OAT_locked_prediction.json` was recorded before this verification rerun. Earlier exploratory sensitivity results already existed; this is a reproducibility check.

Selected run: higher EBIT margin in 2027-2031, with revenue growth and every other independent input at base.

| Output | Locked prediction | Verification run | Locked change from base | Run change from base | Reconciled |
|---|---:|---:|---:|---:|---|
| 2031 EBIT ($bn) | 19.252 | 19.252 | +1.834 | +1.834 | YES |
| 2031 FCFE ($bn) | 13.244 | 13.244 | +1.558 | +1.558 | YES |
| Value/share ($) | 26.78 | 26.78 | +3.10 | +3.10 | YES |

Locked accounting-check prediction: PASS; verification run: PASS. Overall reconciliation at displayed precision: **PASS**.

Selected 2031 statement trace (USD bn): revenue 91.676 x EBIT margin 21% = EBIT 19.252; net income 15.279 + D&A 14.668 - increase in operating NWC 0.035 - capital spending 14.668 - debt repayment 2.000 = FCFE 13.244.
Value/share trace: (PV of explicit FCFE $30.277bn + PV of terminal value $116.476bn) / 5.48bn shares = $26.78.

## Accounting checks and restored base

The model checks the opening and annual balance-sheet gap, nonnegative cash, and nonnegative debt. Invalid runs are flagged and excluded from the output spans. Signed annual FCFE and every statement line are retained in the details file.

| Run | Max absolute balance gap ($bn) | Minimum cash ($bn) | Minimum debt ($bn) | Status |
|---|---:|---:|---:|---|
| Initial base | 0.000000000 | 54.723 | 40.537 | PASS |
| revenue_growth/Lower | 0.000000000 | 54.633 | 40.537 | PASS |
| revenue_growth/Higher | 0.000000000 | 54.813 | 40.537 | PASS |
| ebit_margin/Lower | 0.000000000 | 53.533 | 40.537 | PASS |
| ebit_margin/Base | 0.000000000 | 54.723 | 40.537 | PASS |
| ebit_margin/Higher | 0.000000000 | 55.913 | 40.537 | PASS |
| Restored base | 0.000000000 | 54.723 | 40.537 | PASS |

Restored base equals the first base run across the full linked statement rows, checks, and valuation: **YES**.

If any forecast FCFE is negative or the existing terminal valuation is unavailable, value per share is shown as N/A. The signed annual FCFE path remains in the details file, without a substitute terminal value.

## How to read the sensitivity

One-at-a-time means changing one independent input path while every other independent input starts at base; the linked statements and valuation then recalculate. The output span is the highest minus the lowest valid result among that driver's lower, base, and higher cases.

The ranking depends on the chosen ranges. Both paths here shift by 2 percentage points each year; a different growth or margin range could produce a different ranking. These scenarios have no assigned probabilities, and the table does not estimate how often a case will occur or whether the two drivers will move together.

