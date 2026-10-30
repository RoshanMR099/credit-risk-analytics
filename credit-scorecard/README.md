# Credit Card Default Scorecard

A probability of default (PD) scorecard that predicts whether a credit card client will default next month, then turns the PD into an expected loss and an approval cut-off strategy.

## Data

Default of Credit Card Clients, Taiwan, April to September 2005 ([UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/350), id 350; Yeh and Lien, 2009). 30,000 clients, 23 features, 22.1% default rate. The notebook downloads the data automatically with `ucimlrepo`.

## Approach

- **Model:** logistic regression scorecard (80/20 stratified split).
- **Features:** gender excluded; education and marriage one-hot encoded; the three most recent repayment statuses grouped into categories.
- **Evaluation:** AUC, Gini, KS, ROC curve, decile table and 5-fold cross-validation.
- **Expected loss:** PD x EAD x LGD, with EAD from the latest bill amount and an assumed 45% LGD.
- **Cut-off analysis:** approval rate and default rate at different PD cut-offs.

## Results

| Item | Result |
|---|---|
| Test set (6,000 clients) | AUC 0.751, Gini 0.501, KS 0.385 |
| 5-fold cross-validation | Mean AUC 0.761 (std 0.016) |
| Riskiest 10% of clients | 71.8% predicted vs 68.7% actual default rate |
| Safest 10% of clients | 5.8% predicted vs 5.7% actual default rate |
| Expected loss (NTD) | 30.0M predicted vs 30.3M actual |
| Cut-off at PD below 15% | Approves 51.2% of clients at an 11.2% default rate (22.1% overall) |

**Key finding:** the latest repayment status is the strongest default signal.

## Output

`decile_table.csv` and `cutoff_table.csv`.

## How to run

1. Install Python with pandas, NumPy, scikit-learn, matplotlib and ucimlrepo.
2. Run `uci_credit_default.ipynb` from top to bottom.

## Limitations

- LGD is an assumption because the dataset has no recovery data.
- EAD is approximated by the latest bill amount.
- Data is from one Taiwan card portfolio in 2005, used to demonstrate the method.


