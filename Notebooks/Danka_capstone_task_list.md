# Capstone Feedback & Task List — Danka

**Project:** Agricultural Mechanization Price Analysis and Evaluation
**Notebooks reviewed:** `01_EDA_new.ipynb`, `02_modelling_new.ipynb`, `README.md`

---

## What is already working well

- Your cleaning is fully reproducible: every count in the README (6.622 → 3.970 → 3.954 → 3.505) matches the raw data.
- You kept the original Excel unchanged, preserved the 37 evaluation notes in their own column, and recorded duplicate frequencies before dropping them. These are exactly the right habits.
- You validated your deviation calculation against the original column.
- You were right not to merge `Mechanization.xlsx`: the application totals do not line up with the price lines.
- Your limitations section already names the two biggest issues (similar equipment in train and test, and dependence on the quoted price). The tasks below turn those limitations into analysis.

---

## How to use this list

Tasks are grouped by notebook and tagged by priority:

- 🔴 **Must** — changes what your results mean; do these first.
- 🟠 **Should** — strengthens the evidence for your conclusions.
- 🟢 **Nice to have** — polish if time allows.

Each task has a **Why**, a **What to do**, starter code where useful, and a **Done when** check so you know when to move on.

---

## Part A — EDA (`01_EDA_new.ipynb`)

### A1. 🔴 Decode the committee's final-price rule

**Why:** Your dataset contains `Final Price Proposed by Evaluation Committee (EUR)` and the reference +10% / +15% / +30% columns. Together they describe what the committee actually did with each quote. It is easy to treat these as "derived columns to exclude" and move on, but they tell you whether ±30% really means *accepted vs rejected*.

**What to do:**
1. Group records by deviation band and check whether the final price equals the quote or something else.
2. Form a hypothesis for the rule (hint: compare the final price with `min(quote, reference × something)`, and check whether "something" depends on the price level).
3. Test the hypothesis on all records and look at the exceptions.

```python
band = pd.cut(prices_analysis["Price Deviation (%)"],
              [-np.inf, 0, 10, 15, 30, np.inf],
              labels=["at/below ref", "0–10%", "10–15%", "15–30%", "over 30%"])

accepted = np.isclose(prices_analysis[FINAL], prices_analysis[QUOTE], rtol=1e-3)
pd.crosstab(band, np.where(accepted, "quote accepted", "quote changed"))
```

**Done when:** you can state the committee's rule in one sentence, it reproduces almost all final prices (you should get above 99%), and you have written a short markdown note on what this means for your ±30% question.

---

### A2. 🔴 Make the research question one-sided

**Why:** Of your 612 "outside ±30%" records, some are quotes far *below* the reference. A low quote is not a budget risk for the programme. Counting it as "unacceptable" mixes two very different situations.

**What to do:**
1. Count how many outside-range records are above +30% and how many are below −30%.
2. Check (using A1) how the committee treated the below −30% quotes.
3. Create a one-sided target column and use it from here on.

```python
prices_analysis["Over 30%"] = (prices_analysis["Price Deviation (%)"] > 30).astype(int)
```

**Done when:** your question, Summary and Key findings all refer to *over-pricing* (quote more than 30% above the reference), and the README table is updated to match.

---

### A3. 🔴 Check how often the same equipment is priced again

**Why:** This is the most important check for your modelling. If the committee reuses the same reference price for the same item, a random train/test split will put identical items in both sets, and the model can score very well simply by remembering prices.

**What to do:**
1. Build an item identifier from equipment, brand and model (lower-case and strip spaces first so small typing differences don't create "new" items).
2. Count how many records belong to items that appear more than once.
3. For repeated items, check how often the reference price is identical every time.

```python
def normalise(s):
    return s.fillna("Unknown").str.lower().str.replace(r"\s+", " ", regex=True).str.strip()

prices_analysis["item_id"] = (normalise(prices_analysis["Equipment / Item"]) + " | " +
                              normalise(prices_analysis["Manufacturer / Brand"]) + " | " +
                              normalise(prices_analysis["Model / Specifications"]))

per_item = prices_analysis.groupby("item_id")[REF].agg(["count", "nunique"])
repeated = per_item[per_item["count"] > 1]
print("Share of repeated items with one single reference price:",
      (repeated["nunique"] == 1).mean())
```

**Done when:** you have the two percentages, a markdown interpretation ("the reference price behaves like a …"), and `item_id` is exported in the processed CSV for the modelling notebook.

---

### A4. 🟠 Re-draw the quoted vs reference scatter on a log scale

**Why:** On the raw EUR scale almost all points are squeezed into one corner by a few tractors. Because your question is about *percentages*, a log scale is the natural view: the ±30% band then has the same width at every price.

**What to do:** Plot both axes on a log scale, add the lines quote = reference, +30% and −30%, and colour the over-priced records.

```python
plt.scatter(df[REF], df[QUOTE], s=8, alpha=0.4,
            c=df["Over 30%"].map({0: "tab:blue", 1: "tab:red"}))
line = np.logspace(1, 5.4, 50)
plt.plot(line, line, "k-"); plt.plot(line, line * 1.3, "k--"); plt.plot(line, line * 0.7, "k:")
plt.xscale("log"); plt.yscale("log")
```

**Done when:** the chart is in the bivariate section with a 2–3 sentence interpretation, and you note what the log view suggests for the regression target (see B3).

---

### A5. 🟠 Explore where over-pricing happens

**Why:** `Supplier` is known when an application arrives, but it is not explored in the EDA or used in modelling. Over-pricing rates may differ a lot between suppliers and price bands.

**What to do:**
1. Over-pricing rate by supplier (only suppliers with at least ~40 records).
2. Over-pricing rate by quoted price band.
3. Optionally, the equipment types with the highest rates.

**Done when:** you have one or two bar charts with interpretation, and a decision in "Decisions carried forward" on whether `Supplier` becomes a feature.

---

### A6. 🟢 Smaller data-quality notes

- **Unit prices.** Look at the lowest reference prices (`nsmallest`). Some lines appear to be priced per metre or per piece. Add a short note.
- **"Duplicates" may be quantities.** Identical lines within the same `No.` (application) are probably several units of the same item. Your `Record Frequency` column already preserves this; mention the interpretation.
- **Untranslated notes.** "REALNA" / "realnA" means "realistic" and belongs with "Reasonable price". Harmonise the labels.
- **Wording.** The notebook question still says "regional reference price", but no regional data is used. Align it with the README.

**Done when:** each point has a one-line note or fix in the data quality section.

---

### A7. 🔴 Update "Decisions carried forward"

**Done when:** this section states the one-sided target (A2), the catalogue finding and its consequence for train/test splitting (A3), the log-scale target (A4), and the supplier decision (A5). The feature-selection cells at the end of the EDA can move to the modelling notebook, so the EDA ends with the export.

---

## Part B — Modelling (`02_modelling_new.ipynb`)

### B1. 🔴 Add a catalogue-lookup baseline

**Why:** The mean-price baseline is so weak that any model beats it. After A3, the honest baseline is "use the reference price this item received before". Any model has to beat *that* to be worth using.

```python
catalogue = train.groupby("item_id")[REF].last()
lookup = test["item_id"].map(catalogue)
seen = lookup.notna()
print("Test rows with a catalogue price:", seen.mean())
print("Lookup MAE on those rows:", mean_absolute_error(test.loc[seen, REF], lookup[seen]))
```

**Done when:** the lookup appears in your comparison table, with its coverage (share of test rows it can price) and its decision results for those rows.

---

### B2. 🔴 Evaluate separately on items seen before vs new items

**Why:** Your R² of 0,99 combines two very different situations. To know whether the model can judge equipment the committee has *not* priced before, every test item must be absent from training. `GroupKFold` does exactly this (it extends the `KFold` idea from the cross-validation notebook).

**What to do:**
1. Split your Experiment 1 test results into "item seen in training" vs "new item" and report errors and false acceptances for each.
2. Re-run your models with `GroupKFold`, using `item_id` as the group.

```python
from sklearn.model_selection import GroupKFold, cross_val_predict

gkf = GroupKFold(n_splits=5)
oof_pred = cross_val_predict(ridge_model, X, y, cv=gkf, groups=df["item_id"])
```

**Done when:** you can say, with numbers, how the best model performs on new equipment specifically, and how many over-priced quotes it catches there.

**Check yourself:** look at the Ridge coefficient on the quoted price (divide by the scaler's `scale_` to get EUR per EUR). What does its size tell you about why over-priced quotes look acceptable?

---

### B3. 🔴 Revisit Experiment 3: an independent reference price

**Why:** Your Experiment 3 asked the right question — can the reference be estimated without the farmer's offer? — but the EUR-scale target produced negative predictions and large percentage errors.

**What to do:**
1. Fit on a log target so errors are relative and predictions stay positive:
   ```python
   from sklearn.compose import TransformedTargetRegressor
   model = TransformedTargetRegressor(regressor=Ridge(), func=np.log1p, inverse_func=np.expm1)
   ```
2. Replace the one-hot `Model / Specifications` column with features that can generalise to unseen models, for example numbers with units extracted from the text (cm, l, kW, hp, t) and/or `TfidfVectorizer(analyzer="char_wb", ngram_range=(2, 4))` on the combined description.
3. Evaluate with `GroupKFold` (B2) and report **median percentage error** and **share of predictions within ±10% and ±30%**, not only MAE and R².

**Done when:** you have a comparison table (original Experiment 3 vs log target vs log + text features) and a clear statement of whether the error is small enough to make a ±30% decision on its own.

---

### B4. 🟠 Predict over-pricing directly

**Why:** Predicting a price and then thresholding it is an indirect route to the decision. A classifier that predicts `Over 30%` directly can use the quote without circularity: the model learns how high a quote is *compared with what similar items usually cost*.

**What to do:**
1. Logistic regression with item features + `log(quote)` (+ supplier if A5 supports it), `class_weight="balanced"`.
2. Evaluate with `GroupKFold`: ROC-AUC, PR-AUC, recall and precision for the over-priced class.
3. Choose the probability threshold from cross-validated probabilities on the **training data only**, aiming for a recall target you can justify (e.g. 80%).
4. Compare with your regression-then-threshold approach on the same folds.

**Done when:** you have a confusion matrix and a short paragraph explaining the trade-off between caught over-priced quotes and extra lines sent for review.

---

### B5. 🟠 Make model comparisons stable

**Why:** Your Experiment 2 conclusion rests on 63 vs 53 false acceptances from one split. It is easy to over-read a single split: re-run Experiment 1 with `random_state` 0–9 and see how much the false-acceptance count moves for the *same* model.

**Done when:** every model comparison in the notebook uses cross-validation or repeated splits, and the Experiment 2 conclusion is re-checked against that variation.

---

### B6. 🟠 Report decision metrics, and euros

**Why:** The committee's question is "which quotes need attention?", not "what is the RMSE?". Using A1, you can also measure the euros involved: the committee's cut on each over-priced line is `quote − capped final price`.

**What to do:** for each approach, report over-priced quotes caught (recall), false flags, number of lines sent to manual review, and euros of committee cuts the system would have let through.

**Done when:** your final comparison table includes these columns, and your interpretation is based on them rather than R².

---

### B7. 🟢 Tune hyperparameters inside the pipeline

**What to do:** use `GridSearchCV` with your `GroupKFold` object (`cv=gkf`, and pass `groups=` to `.fit`) to tune Ridge `alpha` and logistic regression `C`. Random Forest does not need `StandardScaler`; it is harmless, but you can remove it for clarity.

**Done when:** tuned values are reported and used in the final comparison.

---

## Part C — README and conclusions

- 🔴 Update the research question and findings to the one-sided version (A2).
- 🔴 Add the committee rule (A1) and the catalogue finding (A3) to Main EDA Findings.
- 🔴 Replace the R²-led results with the seen-item vs new-item results and decision metrics (B2, B6).
- 🟠 Rewrite the conclusion so it answers the question separately for **equipment priced before** and **new equipment**.
- 🟠 Move "similar equipment in train and test" from Limitations into Results, since you have now measured it.
- 🟢 Fix the run instructions: they refer to `01_EDA.ipynb` / `02_modelling.ipynb`, while the reviewed notebooks are the `_new` versions. Consider removing or archiving the older versions so reviewers open the right files.

---

## Course materials to revisit

| Task | Notebook |
|---|---|
| A3, B2, B5 | `02-train-test-split-cross-validation` |
| B3 | `02-regression-metrics`, `02-regularization`, `02-model-workflow` (log transforms) |
| B4 | `02-logistic-regression`, `02-classification-metrics`, `03-roc-auc`, `02-imbalanced-data` (option 4: change the threshold) |
| B7 | `02-gridsearch-pipelines`, `03-gridsearch-pipelines-practice` |
| B6 | `calculating-roi` (costing the trade-off) |

`GroupKFold`, `TransformedTargetRegressor` and `TfidfVectorizer` go slightly beyond the course notebooks. Check the scikit-learn documentation for each, as parameter names can change between versions.

---

## Final checklist

- [ ] A1 Committee rule decoded and explained
- [ ] A2 Question and target are one-sided (over-pricing)
- [ ] A3 Item repetition measured; `item_id` exported
- [ ] A4 Log-scale scatter with ±30% bands
- [ ] A5 Supplier / price-band over-pricing explored
- [ ] A6 Data-quality notes added
- [ ] A7 Decisions carried forward updated
- [ ] B1 Catalogue-lookup baseline
- [ ] B2 Seen vs new items; `GroupKFold`
- [ ] B3 Independent reference estimate (log target, better text features)
- [ ] B4 Direct over-pricing classifier with tuned threshold
- [ ] B5 Comparisons use CV or repeated splits
- [ ] B6 Decision metrics and euros reported
- [ ] B7 Hyperparameters tuned
- [ ] C README and conclusions updated
