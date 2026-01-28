# Lending-club-loan-defaults-investment-strategy

# Lending Club Loan Default Prediction & Profit-Optimized Investment Strategy

Predict loan default risk (Fully Paid vs Charged Off) and translate model outputs into an **investment decision rule** that maximizes expected profit under a simplified $100-per-loan strategy.

---

## 1) Problem Statement

Peer-to-peer (P2P) lending platforms expose investors to **credit default risk**. The business need is not just “high accuracy,” but **better investment decisions**:
- Invest in loans likely to be **Fully Paid**
- Avoid loans likely to be **Charged Off**
- Maximize **expected portfolio profit** vs a safe alternative (e.g., CDs)

This project builds predictive models on Lending Club loan data and evaluates them using both:
- **ML classification metrics** (Confusion Matrix, ROC/AUC, Lift)
- **Business metrics** (Expected Profit/Loss from investment decisions)

Assignment context: Online lending / marketplace lending decision support. :contentReference[oaicite:0]{index=0}

---

## 2) Objectives

### A. Loan Status (Classification)
Predict:
- **loan_status ∈ {Fully Paid, Charged Off}**

Evaluate using:
- Confusion Matrix
- ROC Curve + AUC
- Lift (ranking quality for targeting)

(These are explicitly required performance measures in the case prompt.) :contentReference[oaicite:1]{index=1}

### B. Investment Strategy (Business Evaluation)
Convert predictions into “invest / don’t invest” decisions and compute total profit based on a profit/loss model.

From the project’s derived business assumptions:
- **Profit per correctly invested Fully Paid loan ≈ +$8 per $100**
- **Loss per mistakenly invested Charged Off loan ≈ −$12 per $100** :contentReference[oaicite:2]{index=2}

---

## 3) Data

### Dataset
Lending Club historical loan-level attributes (borrower, loan terms, credit metrics, etc.), used to model default risk and investment outcomes. :contentReference[oaicite:3]{index=3}

### Feature categories (high level)
- Loan info: loan_amnt, int_rate, term, grade/sub_grade, purpose, installment
- Borrower info: annual_inc, emp_length, home_ownership, dti, fico ranges
- Timeline fields: issue date, credit history dates
- Derived metrics engineered for risk signal

(Example categorization captured in report.) :contentReference[oaicite:4]{index=4}

---

## 4) Key Modeling Decisions

### 4.1 Train/Test Split
- **70% train / 30% validation**, to balance learning capacity and evaluation reliability. :contentReference[oaicite:5]{index=5}

### 4.2 Leakage Control (Critical)
Exclude variables that wouldn’t exist at decision time (pre-funding) or are updated after loan origination, e.g.:
- repayment fields, post-funding balance, hardship/settlement variables, last payment dates, etc. :contentReference[oaicite:6]{index=6}

This is essential because “too good to be true” performance often indicates leakage.

### 4.3 Missing Values
Drop identifiers / privacy fields and handle missingness by:
- imputing sensible defaults (e.g., delinquency months missing → no delinquency → 0)
- excluding variables with excessive missingness :contentReference[oaicite:7]{index=7}

---

## 5) Models Used

### Classification (Default Prediction)
1. **Decision Tree (rpart)**
   - Tuned complexity parameter (cp), minsplit, pruning
2. **Random Forest**
   - Tuned `ntree` (e.g., 200 vs 500) and compared performance
3. **Boosted Trees (XGBoost / GBM)**
   - Tuned iterations / trees and evaluated ranking quality (AUC/Lift)

These model families are explicitly aligned to the case requirements. :contentReference[oaicite:8]{index=8}

### Return Modeling (Regression)
- **GLM**
- **Random Forest**
- **GBM**
Evaluated primarily via **RMSE** for return prediction. :contentReference[oaicite:9]{index=9}

---

## 6) Evaluation

### 6.1 ML Metrics
- Confusion Matrix: error types (FP/FN) matter for investment
- ROC/AUC: class separation
- Lift: prioritization quality for “top-ranked loans first” targeting

(These measures are required and explained as important for this decision problem.) :contentReference[oaicite:10]{index=10}

### 6.2 Business Metric: Profit-Based Model Selection

**Assumptions**
- Invest $100 in each loan predicted **Fully Paid**
- Use:
  - **+$8 profit** for correct Fully Paid prediction
  - **−$12 loss** for incorrect Fully Paid prediction on Charged Off loans :contentReference[oaicite:11]{index=11}

**Total Profit Results (Validation/Test)**
- Decision Tree (rpart): **$252,384**
- Random Forest: **$263,978** ✅ Best
- XGBoost: **$131,082** :contentReference[oaicite:12]{index=12}

**Interpretation**
Random Forest produced the best profit under this simplified investing rule, so it is the recommended model when the goal is profit—not just AUC.

---

## 7) Final Recommendation (How to Invest)

A pure “only lower-grade loans” approach is risky even if returns are higher on average.

Recommended approach:
- Use **high-grade (A/B)** loans for stability and lower default risk
- Add a **screened subset of C/D/E** loans only if:
  - high predicted Fully Paid probability (status model)
  - high predicted return (return model)

This combined strategy is explicitly recommended in the report:
- **GLM for loan-status + GBM for returns** as a balanced decision framework :contentReference[oaicite:13]{index=13} :contentReference[oaicite:14]{index=14}


