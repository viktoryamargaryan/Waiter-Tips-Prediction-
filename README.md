# Waiter Tips Prediction

## Project Overview
This project predicts the amount of tip a waiter is likely to receive based on details of a restaurant bill: total bill amount, customer gender, smoking status, day of the week, time (lunch/dinner), and party size. The goal is to identify which factors most influence tip size and build a simple regression model that estimates a tip for a given set of inputs.

## Getting Started
1. Clone this repository:
   ```bash
   git clone https://github.com/YOUR_USERNAME/waiter-tips-prediction.git
   cd waiter-tips-prediction
   ```
2. Install the required libraries:
   ```bash
   pip install pandas numpy plotly scikit-learn matplotlib
   ```
3. Make sure `tips.csv` is in the same folder as the notebook/script.

## How to Run and Test
- Open `waiter_tips_prediction.ipynb` in Jupyter Notebook or Google Colab and run all cells top to bottom (Runtime → Run all).
- Alternatively, run the script version directly:
  ```bash
  python waiter_tips_prediction.py
  ```
- The notebook/script prints the R² score on the test set and a sample prediction at the end, so you can confirm it runs correctly.

## Libraries and Functions Used
- **pandas** — loading and manipulating the `tips.csv` dataset (`read_csv`, `map`, `groupby`)
- **numpy** — numeric array handling for the prediction example
- **plotly.express** — interactive visualization (`scatter`, `pie`) to explore relationships between features
- **scikit-learn** — `train_test_split` to split data into training/test sets, `LinearRegression` to train the model, and `model.score()` / `model.predict()` to evaluate and predict
- **matplotlib** — used to generate the static chart images embedded below, since GitHub cannot render interactive Plotly output

## Dataset
- Source: `tips.csv` (244 records, no missing values)
- Features used:
  - `total_bill` — total bill in dollars (numeric)
  - `sex` — gender of the person paying (Female/Male, encoded 0/1)
  - `smoker` — whether the party included a smoker (No/Yes, encoded 0/1)
  - `day` — day of the week (Thur/Fri/Sat/Sun, encoded 0–3)
  - `time` — Lunch/Dinner, encoded 0/1
  - `size` — number of people in the party (numeric)
- Target: `tip` — tip given in dollars

## Exploratory Data Analysis
Using `plotly.express`, scatter plots of `tip` vs `total_bill` (colored by day, gender, and time) show a clear positive linear relationship: larger bills lead to larger tips, roughly in the 15–20% range. Pie charts of tip share by day, gender, and smoking status show Saturday accounts for the largest share of total tips, and tipping behavior is broadly similar across gender and smoking status, with total_bill and party size standing out as the strongest visual predictors.

**Tips vs Total Bill, by day:**
![Tips vs Total Bill by day](images/fig1_bill_vs_tip_day.png)

**Tips vs Total Bill, by gender:**
![Tips vs Total Bill by gender](images/fig2_bill_vs_tip_sex.png)

**Tips vs Total Bill, by time (Lunch vs Dinner):**
![Tips vs Total Bill by time](images/fig3_bill_vs_tip_time.png)

**Share of total tips by day of the week:**
![Share of tips by day](images/fig4_pie_day.png)

**Share of total tips by gender:**
![Share of tips by gender](images/fig5_pie_sex.png)

**Share of total tips by smoking status:**
![Share of tips by smoker status](images/fig6_pie_smoker.png)

## Model Choice
**Linear Regression** was chosen because:
- The relationship between `total_bill` and `tip` is visibly linear (confirmed by the trendlines in the EDA scatter plots).
- The target (`tip`) is a continuous numeric value, making this a regression problem rather than classification.
- Linear Regression is simple, fast to train, and highly interpretable — its coefficients directly show how much each feature contributes to the predicted tip, which is valuable for understanding tipping behavior.
- The dataset is small (244 rows) and doesn't show strong non-linear patterns, so a more complex model isn't justified.

## Training Method
- Features and target were split using `train_test_split` from scikit-learn with `test_size=0.2` (80% train / 20% test) and `random_state=42` for reproducibility.
- A `LinearRegression` model from scikit-learn was fit on the training data (`model.fit(xtrain, ytrain)`).

## Evaluation
- **R² score on the test set: 0.44**
- This means the model explains about 44% of the variance in tip amounts. The remaining variance is likely due to factors not captured in the dataset (e.g., quality of service, customer generosity, mood).
- Model coefficients (approx.):
  - `total_bill`: +0.094 — each extra dollar on the bill increases predicted tip by ~9.4 cents
  - `size`: +0.232 — each additional guest increases predicted tip by ~23 cents
  - `smoker`: -0.181 — smoking parties tend to tip slightly less
  - `time`: -0.193 — dinner tips trend slightly lower than lunch, holding other factors constant
  - `day`, `sex`: relatively minor effects

## Example Prediction
For a $24.50 bill, male payer, non-smoker, Saturday, Dinner, party of 4:
```python
features = pd.DataFrame([[24.50, 1, 0, 2, 1, 4]], columns=x.columns)
model.predict(features)  # -> $3.87
```

## Conclusion
`total_bill` and `size` are the strongest predictors of tip amount, consistent with common intuition (bigger bill and bigger group → bigger tip). Linear Regression, while simple, provides a reasonably interpretable baseline with an R² of 0.44. Future improvements could include trying regularized models (Ridge/Lasso) or non-linear models (Random Forest) to see if accuracy improves.
