
# Mortgage Expected Loss and Stress Test (CECL-style)

Estimates the 24-month probability of default (PD) for US mortgages, converts it into expected credit loss (ECL), and stresses the result under a severe house-price decline.

## Data

Freddie Mac Single-Family Loan-Level Dataset, 2018 vintage sample: 50,000 mortgages and about 2.09M monthly performance records. The data is free after registering on Freddie Mac's Clarity site but is licensed for internal use, so it is **not included** in this repo.

## Approach

- **Default definition:** 90+ days past due (or foreclosure) within the first 24 months of the loan.
- **Cleaning:** placeholder codes (9999, 999) converted to missing values.
- **Model:** logistic regression on credit score, LTV, DTI, interest rate, original balance, number of borrowers, loan purpose, occupancy and first-time-buyer flag (70/30 stratified split).
- **Validation:** AUC, Gini, KS, decile calibration, and a decision tree as challenger model.
- **Expected loss:** ECL = PD x LGD x EAD, with LGD tested at 20%, 30% and 40%.
- **Stress test:** house prices fall 30%, so LTV becomes LTV / (1 - 0.30). Loans are re-scored with the same model.

## Results

| Item | Result |
|---|---|
| 24-month default rate | 2.67% |
| Logistic model, test set | AUC 0.769, Gini 0.539, KS 0.406 |
| Decision tree challenger, test AUC | 0.720 |
| Predicted vs actual default rate | 2.61% vs 2.67% |
| Baseline ECL (30% LGD) | $94.6M, 0.82% of $11.58B exposure |
| Stressed ECL (house prices -30%) | $147.9M, 1.28% of exposure (+56.4%) |

## Output

`CECL_Summary.xlsx` with three sheets: Scenario ECL, Calibration, LGD sensitivity.

## How to run

1. Install Python with pandas, NumPy, scikit-learn and openpyxl.
2. Register on Freddie Mac's Clarity site and download `sample_orig_2018.txt` and `sample_perf_2018.txt`. Place both files next to the notebook. Do not redistribute them.
3. Run `freddie_mac_cecl_expected_loss_stress_test.ipynb` from top to bottom.

## Limitations

- This is a CECL-*style* framework, not a full CECL model.
- LGD is an assumption; no recoveries were estimated.
- EAD uses the original balance, and ECL is calculated in-sample.
- Only the housing channel is stressed; unemployment is not modelled.
- Loans paid off early are counted as non-defaults.

