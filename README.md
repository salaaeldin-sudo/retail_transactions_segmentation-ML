# Customer Segmentation — RFM Analysis & K-Means Clustering

01-ml-project — Arabian Academy AI Engineering Track

**Group:** `<group-id>` — replace with your real ID (e.g. `a1-g07`, `a3-g03`) before submitting.

## Overview

Segments 300 retail customers into actionable personas using RFM (Recency, Frequency, Monetary) features, K-Means clustering, and PCA visualization.

## Dataset

- **File:** `01-ml-project/retail_transactions_segmentation.csv`
- **Shape:** 3,663 transactions across 300 unique customers
- **Columns:** `customer_id`, `transaction_date`, `amount`, `product_category`

## Approach

1. **Data cleaning & validation** — checked for missing values, duplicate rows/transactions, non-positive amounts, unparseable dates, and inconsistent category labels. Also scanned `amount` for outliers with an IQR fence. Result: the dataset is clean; no records needed to be dropped or fixed.
2. **RFM feature table** — one row per customer:
   - *Recency*: days since last purchase, measured from a fixed reference date of `2024-12-31` (one day after the dataset's last transaction, so no customer sits at exactly 0).
   - *Frequency*: number of transactions.
   - *Monetary*: **total** spend per customer (not average).
3. **Scaling** — `StandardScaler` on all three RFM features before clustering, since they sit on very different numeric scales (days vs. counts vs. currency).
4. **Choosing k** — swept `k = 2..10`, comparing the elbow (inertia) curve against the silhouette score.
5. **K-Means** — fit with `random_state=42` for reproducibility.
6. **PCA** — reduced the scaled features to 2 components (~99% of variance retained) for a 2D scatter plot of the clusters.
7. **Personas** — named from each cluster's actual RFM averages, not assigned arbitrarily.

## Why k = 4

Silhouette score is technically highest at k=2 (0.68) and nearly tied at k=3 (0.68), but both are misleading: k=2 puts 87% of customers in one undifferentiated bucket, and k=3 still leaves a 79% catch-all cluster. The elbow curve flattens sharply after k=4, and at k=4 the silhouette score (0.57) still reflects real, moderately well-separated structure — while producing four segments that are each large enough to matter and genuinely different in behavior.

## Results

| Persona | Customers | Share | Recency (days) | Frequency | Monetary (total) |
|---|---:|---:|---:|---:|---:|
| VIP / Champions | 40 | 13% | ~8 | ~35 | ~$29,684 |
| Core / Loyal Regulars | 128 | 43% | ~20 | ~14 | ~$4,700 |
| At-Risk / Occasional | 111 | 37% | ~72 | ~4 | ~$753 |
| Dormant / Lost | 21 | 7% | ~263 | ~2 | ~$321 |

**Recommended actions:** reward and protect VIPs; nurture Core customers toward VIP status with loyalty/cross-sell offers; run win-back campaigns for At-Risk customers before they fully disengage; use only low-cost, automated reactivation for the Dormant group.

## Running the Notebook

**Requirements:** `pandas`, `numpy`, `scikit-learn`, `matplotlib`

1. Open `<group-id>_01-ml-project.ipynb` in Jupyter.
2. Restart & Run All.
3. The data-loading cell tries several relative paths automatically (and falls back to searching parent directories), so it should find `retail_transactions_segmentation.csv` whether the notebook is opened from its submission location or from the repo root.

## File Structure

```
submissions/<cohort>/<group-id>/01-ml-project/
└── <group-id>_01-ml-project.ipynb
```

## Note on submission

`SUBMISSION_CRITERIA.md` §1 lists *exactly* the notebook (plus `<group-id>_recap.md` for non-spotlighted A1 groups) as the required submission files. This README isn't part of that list — keep it outside your graded `submissions/` folder (e.g. in your own fork/portfolio) unless your mentor says otherwise.
