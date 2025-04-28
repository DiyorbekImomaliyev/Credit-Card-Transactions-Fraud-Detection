# Credit Card Fraud Detection: Exploratory Data Analysis (EDA)# Credit Card Fraud Detection - EDA

## TL;DR

This EDA uncovers patterns in credit card fraud, highlighting key indicators such as transaction amounts, temporal trends, and customer behavior.  
The findings guide preprocessing and feature engineering to build a robust fraud detection model.

---

## Dataset Overview

**Source**: Credit card transactions with fraud labels.  
**Target**: `is_fraud` (binary: 0 = legitimate, 1 = fraudulent)

### Columns Retained After Preprocessing

- **Numerical (16)**: `amt`, `distance`, `age`, `cust_trans_count`, etc.
- **Categorical (7)**: `cc_num`, `merchant`, `category`, `gender`, etc.

### Preprocessing Steps

- **Dropped**:
  - Sensitive PII: `first`, `last`, `street`, `city`
  - Redundant IDs: `trans_num`
  - Raw timestamps: `trans_date_trans_time`

- **Engineered Features**:
  - **Temporal**: `hour`, `dayofweek`, `day` (extracted from `trans_date_trans_time`)
  - **Geospatial**: `distance` (Haversine formula between cardholder and merchant)
  - **Aggregated**: Customer and merchant transaction counts, average amounts, fraud rates

---

## Key Insights

### 1. Transaction Amount (`amt`)

- Extreme right skew: Median = \$47.52, Maximum = \$28,948
- Fraudulent transactions have higher median amounts than legitimate transactions
- Outliers (amount > \$193) are rare but not exclusively fraudulent

### 2. Temporal Patterns

- Peak fraud hours: 12 AM–3 AM and 10 PM–11 PM (2–3x higher fraud rates)
- Low-risk window: 4 AM–9 PM

### 3. Transaction Categories

- Categories with highest fraud rates:
  - `shopping_net` (2.0%)
  - `misc_net` (1.0%)
  - `grocery_pos` (1.0%)

### 4. Customer Behavior

- High-risk profiles: Customers with fewer transactions (<500) but high average transaction amounts (\$400–\$800)

### 5. Geospatial Signals

- Distance between customer and merchant shows no strong fraud signal (median ~76 km for both classes)

### 6. Demographics

- Gender: Men have slightly higher fraud rates (1.0% vs. 0.9% for women)
- States with unusually high fraud rates:
  - Delaware (DE): 100% (very low transaction count, 9 transactions)
  - Rhode Island (RI): 2.7%
  - Alaska (AK): 1.7%

---

## Data Quality and Modeling Implications

### Class Imbalance

- Severe imbalance: 99.4% non-fraud, 0.6% fraud
- Requires techniques like SMOTE, oversampling, or class-weighted algorithms

### Feature Engineering Wins

- High-value engineered features:
  - `cust_fraud_rate`
  - `hour`
  - `distance`

### Outlier Handling

- Log-transform `amt` to address skewness
- Contextual analysis required to separate legitimate high-value transactions from fraud

---

## Visualizations

Key plots generated during EDA (available in `media/` folder):

- **`unique_values_bar.png`**:  
  - High cardinality in `cc_num` (~1000 cards) and `merchant` (~700 merchants)
  - Suggests using target encoding over one-hot encoding

- **`fraud_rate_by_hour.png`**:  
  - Fraud spikes at night (12 AM–3 AM), indicating temporal risk patterns

- **`boxplots_fraud_status.png`**:  
  - Log-scaled `amt` shows fraud transactions dominate higher quartiles
  - `distance` distribution overlaps between fraud and non-fraud

- **`top_10_category_fraud.png`**:  
  - `shopping_net` leads fraud rates, highlighting vulnerabilities in online retail transactions

---

## Final Thoughts

Fraud detection requires connecting contextual signals across features:  
- A \$500 transaction at 2 AM from a merchant 200 km away raises suspicion.
- The same transaction at 2 PM at a commonly visited store appears legitimate.

### Actionable Takeaways

1. **Feature Synergy Matters**  
   Combine `amt`, `hour`, and `cust_fraud_rate` — signals strengthen when used together.

2. **Class Imbalance Should Be Strategically Managed**  
   99.4% non-fraud is not noise; use metrics like precision-recall AUC, not raw accuracy.

3. **Distribution Shapes Are Signals, Not Problems**  
   Fraudsters typically avoid small transactions; extreme skewness in `amt` is informative, not an issue to "fix."

### Closing Note

Good data preparation is about uncovering the underlying behavioral patterns, not forcing the data into artificial symmetry.  
In fraud detection, nuanced patterns, not obvious anomalies, define success.

---
