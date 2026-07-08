# Credit Card Fraud Detection — End-to-End Data Analytics Project

An end-to-end fraud detection pipeline covering exploratory data analysis, feature engineering, machine learning, SQL analytics, and a Power BI dashboard.

## Project Overview
This project analyzes ~1.3 million credit card transactions to detect fraudulent activity. It walks through the full data pipeline: raw data → EDA → feature engineering → ML model → SQL database → business intelligence dashboard.

## Dataset
- Source: [Kaggle - Credit Card Transactions Fraud Detection Dataset by kartik2112](https://www.kaggle.com/datasets/kartik2112/fraud-detection)
- Size: ~1.3M transactions
- Note: Raw CSV files (`fraudTrain.csv`, `fraudTest.csv`) are excluded from this repo due to file size (~340MB, ~145MB). Download them directly from Kaggle to reproduce this project.

## Project Structure
notebooks/       → Jupyter notebook with full EDA, feature engineering, and ML pipeline
models/          → Trained XGBoost model (fraud_model.pkl)
sql_results/     → Exported results from business SQL queries
dashboard/       → Power BI dashboard (.pbix)

## Pipeline

### 1. Exploratory Data Analysis
- Analyzed class imbalance (99% legitimate vs ~0.58% fraud)
- Visualized transaction amount distributions, fraud rates by hour, merchant category, state, and gender
- Built a correlation heatmap to identify relationships between features

### 2. Feature Engineering
- Extracted time-based features: hour, day of week, month, is_night, is_weekend
- Calculated customer age from date of birth
- Computed customer-to-merchant distance using the Haversine formula
- Encoded categorical columns (gender, category, state)
- Applied log transformation to transaction amount to correct skew
- Split data using stratified 80/20 train-test split

### 3. Machine Learning
- Addressed class imbalance using SMOTE (balanced to ~1M rows per class)
- Trained an XGBoost classifier (100 estimators)
- **Result: 92.2% recall** — correctly identified 1,384 of 1,501 fraud cases in the test set, with only 117 missed
- Model saved via joblib (`models/fraud_model.pkl`)

### 4. SQL Analytics (PostgreSQL)
- Loaded cleaned data into PostgreSQL
- Built lookup tables to decode label-encoded columns (category, state, gender) back to readable names
- Created a consolidated `transactions_summary` view joining all lookups
- Ran business queries answering: fraud rate by category, by hour, by state, by gender, by day of week, night vs day, and distance/amount as fraud signals

### 5. Power BI Dashboard
- Connected directly to PostgreSQL
- Built visuals: fraud rate by merchant category, fraud rate by hour of day, night vs day comparison, state-level fraud table, and summary KPI cards

## Key Findings
- **Overall fraud rate: 0.58%** across 1,296,675 transactions
- **Online categories carry the highest risk** — `shopping_net` (1.76%) and `misc_net` (1.45%) show fraud rates 2-3x the platform average
- **Time of day is the strongest behavioral signal** — fraud rate is ~1.5% during overnight hours (12 AM–3 AM) vs ~0.1% during the day, a ~14x difference
- **Transaction amount is a strong fraud predictor** — fraudulent transactions are on average significantly larger (avg log-amount 5.57 vs 3.52 for legitimate transactions)
- **Distance between customer and merchant was NOT a strong standalone signal** in this dataset (76.1km avg for legitimate vs 76.3km for fraud) — a useful negative finding

## Tools Used
- Python (pandas, scikit-learn, XGBoost, imbalanced-learn, joblib)
- PostgreSQL (SQLAlchemy, psycopg2)
- Power BI Desktop
- Jupyter Notebook

## Author
Parth Rajguru
