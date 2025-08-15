# Project title 
- Early Detection & Prevention of Contract Cancellations
  <img width="600" height="412" alt="order_management" src="https://github.com/user-attachments/assets/2dce9e10-f05b-4084-808b-b1cd451dc57e" />
# Objective
- Build a predictive system that identifies users/contracts at high risk of cancellation so product/marketing teams can proactively intervene and reduce churn, improving lifetime value and retention.
# Why we use this project
- Reduces revenue loss by identifying at-risk subscribers early.
- Enables targeted retention actions (discounts, personalized outreach).
- Improves product roadmap decisions by surfacing signals tied to cancellations (usage drop, billing failures, feature gaps).
- Demonstrates ROI via fewer cancellations, improved LTV and better marketing efficiency.
# High-level step-by-step approach
- Define problem — binary classification (will cancel within next k days) or survival analysis (time-to-cancellation). Choose horizon (e.g., cancel within 30 days).
- Data collection & ingestion — join billing, usage, product, customer support, marketing, and CRM data; include timestamps.
- Exploratory Data Analysis (EDA) — understand churn rates, distributions, seasonality, and data quality.
- Preprocessing — handle missing values, correct timestamps, deduplicate, normalize/encode features.
- Feature engineering — create behavioral, billing and contract features (see list below).
- Feature selection — correlation checks, model-based importance, RFE, domain filters.
- Modeling — baseline models → tree ensembles → calibrated models; handle imbalance.
- Evaluation & backtesting — use time-based splits; evaluate business metrics.
- Deployment & monitoring — export model, score in pipeline, monitor drift and performance.
- Action design — integrate predictions into retention campaigns and A/B test interventions.
# Exploratory Data Analysis (EDA) — what to do / visualize
- Target prevalence: churn rate (overall, per-plan, per-region).
- Cohort analysis: retention by signup month, cohort survival curves.
- Time trends: cancellations vs time (weekly/monthly), seasonality.
- Feature distributions: histograms, boxplots for numeric fields (usage, tenure).
- Categorical analysis: cancellation rate by plan type, billing method, device, channel.
- Correlation & missingness maps: heatmaps, null-value summary.
- Customer journeys: align events (first payment, upgrade, support ticket) and examine common paths before cancellation.
- Outliers / errors: impossible timestamps, negative charges, duplicated records.
- Suggested visualizations: retention curves, monthly churn line chart, heatmap of feature correlations, top support reasons bar chart, distribution of days-to-cancel.
# Typical dataset columns (expected)
- user_id / contract_id
- signup_date, contract_start, contract_end, last_payment_date
- billing_plan (monthly/annual), price, discount_flag
- payment_status (ok/failure_count)
- usage_metrics (daily/weekly CPU minutes, notebooks run, GPUs used)
- activity_last_7d/30d, number_of_sessions, avg_session_length
- support_interactions (tickets_count, last_ticket_days_ago, sentiment)
- marketing_touch (last_campaign, trial_flag)
- device/region/language
- canceled_flag (target) and cancellation_date (if available)
# Feature engineering ideas
- Recency / Frequency / Monetary (RFM): last_active_days, sessions_last_7d, total_spend_90d.
- Trend features: slope or % change of usage over last 30 days.
- Payment signals: missed_payments_count, days_since_last_failed_payment.
- Engagement ratios: active_days_ratio = active_days_last_30 / 30.
- Contract features: months_since_start, contract_type_months, auto_renew_flag.
- Support signals: tickets_last_30d, escalation_flag, average_response_time.
- Product interaction: feature_x_used_flag, freq_of_gpu_use.
- Derived time features: day_of_week_of_last_activity, is_end_of_billing_cycle (within 7 days of renewal).
- Aggregations: rolling aggregates (7d, 30d) and lag features for time-series models.
- One-hot / ordinal encode plan types and categorical variables.
- Normalize/scale numeric features (RobustScaler / StandardScaler) for distance-based models.
# Feature selection
- Remove leakage features (e.g., cancellation_reason_text that appears only after cancel).
- Univariate: filter by mutual information / chi-square for categorical.
- Multivariate: correlation thresholding to drop highly collinear features.
- Model-based: train a tree model and take top-k importances (and then test stability).
- Recursive Feature Elimination (RFE) with cross-validation for the final model if needed.
- Validate selected features by monitoring performance on a time-based hold-out.
# Modeling approaches
- Problem framing: usually binary classification (will cancel within horizon). Alternative: survival models (Cox, survival forest) for time-to-cancel.
- Baseline models
- Logistic Regression (with regularization) — interpretable baseline.
- Decision Tree — quick interpretable baseline.
- Stronger models
- Random Forest / XGBoost / LightGBM / CatBoost — usually best for tabular churn.
- Calibrated classifiers (Platt scaling / isotonic) if probability quality matters.
- Neural nets (MLP) or LSTM/Transformer-based sequence models only if you have rich per-user time series and large data (your LSTM notebook is relevant here).
- Class imbalance
- Use class weights, focal loss, or data resampling (SMOTE, ADASYN) carefully — prefer class weights for time-stable performance.
- Evaluate using precision/recall and PR AUC, not just accuracy.
- Hyperparameter tuning
- Use time-aware cross-validation (rolling-window CV) and Bayesian search (Optuna) or GridSearchCV with TimeSeriesSplit where applicable.
- Sample training checklist
- Split data with a time-based holdout (train up to date T, validate on T+1..T+k).
- Standardize numeric features using training set statistics.
- Persist preprocessing pipeline (scalers, encoders) with the model (sklearn Pipeline).
- Log hyperparameters and metrics (MLflow, Weights & Biases).
# Model testing & evaluation
- Metrics to report
- ROC-AUC and PR-AUC (PR-AUC is often more informative when churn is rare).
- Precision @ k (e.g., precision in top 5% predicted risk).
- Recall, F1, confusion matrix at business threshold.
- Lift / gains chart and cumulative gain for campaign planning.
- Calibration plot (are predicted probabilities reliable?).
- Business metrics: expected cancellations avoided per 1,000 interventions, estimated revenue saved.
- Backtesting & robustness
- Backtest predictions on multiple historical periods.
- Validate across segments (plan, region) to detect blind spots.
- Perform stability checks (population stability index) and fairness checks.
# Output & deliverables
<img width="699" height="560" alt="Screenshot 2025-08-15 101635" src="https://github.com/user-attachments/assets/df058208-0c47-4323-9266-485b94c74f6f" />
