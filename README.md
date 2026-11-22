# Agricultural Mechanization Price Analysis and Evaluation

**Data Science Capstone Project | Exploratory Data Analysis and Machine Learning**

## Project Overview

This project investigates whether machine learning can support the Evaluation Committee in assessing farmers' quoted prices for agricultural mechanization.

**Research question:** Is a quoted agricultural mechanization price within ±30% of a reasonable reference price?

The analysis uses historical reference prices established by the Evaluation Committee. Regression models estimate these prices, and the predicted values are compared with farmers' quoted prices using an experimental ±30% threshold.

The results are intended to explore **decision support**, not automatic approval or rejection.

## Data Source

The project uses `Mechanization_prices.xlsx`, a historical Excel dataset provided for this Capstone and authorized for use in the project.

It contains **6.622 rows and 12 columns**, including equipment descriptions, manufacturers, specifications, suppliers, farmers' quoted prices, and Evaluation Committee reference prices.

The original Excel file is available in the [GitHub repository](https://github.com/milatovicdanka90-maker/dsb_capstone).

The exact historical data collection dates are not documented, and the source data is not modified in place.

## Tools

- Python and Google Colab
- pandas and NumPy
- Matplotlib and Seaborn
- scikit-learn (preprocessing pipelines, Linear Regression, Ridge Regression, Random Forest, and evaluation metrics)

## Notebook 01 — Exploratory Data Analysis

The EDA notebook examines data structure, missing values, reference price notes, duplicates, price distributions, and deviations between quoted and historical reference prices.

### Cleaning and Preparation

| Step | Records |
| --- | ---: |
| Original mechanization price dataset | 6.622 |
| Records with the required identifier, equipment description, and quoted price | 3.970 |
| Records without a numeric reference price at this stage | 450 |
| Records after removing 16 exact duplicate rows | 3.954 |
| Final dataset with both numeric quoted and reference prices | **3.505** |

The original reference-price column includes **37 non-numeric evaluation notes**. These were retained in a separate column.

Of the 450 records without numeric reference prices, 37 contain such notes and 413 have no reference information.

The 16 exact duplicate rows were removed after recording their frequencies in `Record Frequency`.

The final cleaned dataset is exported as `prices_analysis_clean.csv` (**3.505 rows and 17 columns**).

The processed CSV is also available in the repository at [`Data/Processed/prices_analysis_clean.csv`](Data/Processed/prices_analysis_clean.csv).

### Main EDA Findings

Price deviation is calculated as:

`Price Deviation (%) = (Quoted Price - Reference Price) / Reference Price × 100`

| Historical comparison | Records | Share |
| --- | ---: | ---: |
| Within ±30% of the historical reference price | 2.893 | 82,5% |
| Outside ±30% | 612 | 17,5% |

Most quoted prices are in the lower price ranges:

- **3.435 records (98,0%)** have quoted prices up to **50.000 EUR**.
- **70 records (2,0%)** exceed that amount.

These findings motivated the modeling experiments.

## Notebook 02 — Modeling

The modeling notebook reads the processed CSV directly from the GitHub repository.

The file was checked against the final EDA export and confirmed to be identical.

**Prediction target:** `Evaluation Committee Reference Price (EUR)`.

The initial models use four features:

1. `Equipment / Item`
2. `Manufacturer / Brand`
3. `Model / Specifications`
4. `Applicant Quoted Price (EUR)`

Missing categorical values are filled with `Unknown`.

Categorical features are one-hot encoded, and the numeric feature is scaled within the modeling pipelines.

Columns derived from reference prices and historical deviation outcomes are not used as predictors.

### Experiment 1 — Equipment Quoted Up to 50.000 EUR

Linear Regression, Ridge Regression, and Random Forest were compared using **3.435 records**, with **2.748 training** and **687 test** records.

A mean-price baseline provided a benchmark.

| Model | Test MAE (EUR) | Test RMSE (EUR) | R² |
| --- | ---: | ---: | ---: |
| Mean-price baseline | 6.867,39 | — | — |
| Linear Regression | 556,75 | 1.770,56 | 0,9661 |
| Ridge Regression | **308,08** | **791,98** | **0,9932** |
| Random Forest | 319,76 | 892,17 | 0,9914 |

Ridge Regression had the lowest test MAE in this experiment.

However, its ±30% classification matched **600 of 687** historical comparisons (**87,34%**) and produced **63 false acceptances** and **24 false rejections**.

Of the **134** actually outside-range records, **63** were incorrectly marked within range.

Therefore, a low regression error does not by itself guarantee a safe price-evaluation decision.

### Experiment 2 — Complete Dataset

Ridge Regression was trained using all **3.505 records** (**2.804 training** and **701 test**).

The original 687 lower-priced test records were retained for a fair comparison, and 14 higher-priced records were added to the test set.

On the full 701-record test set, Ridge Regression achieved:

- **MAE:** 426,98 EUR
- **RMSE:** 1.380,89 EUR
- **R²:** 0,9926

On the *same 687 lower-priced test records*, the Ridge model trained on 98% of the dataset had an MAE of **308,08 EUR**, compared with **322,46 EUR** for the model trained on the complete dataset.

However, false acceptances decreased from **63 to 53**, while false rejections increased from **24 to 32**.

For the **14 higher-priced test records**, the complete-data model had an MAE of **5.556,40 EUR**, with zero false acceptances or false rejections under the experimental threshold.

This sample is too small to establish reliable performance on higher-priced equipment.

### Experiment 3 — Without the Farmer's Quoted Price

A Ridge Regression model was trained using only equipment type, manufacturer, and model specifications.

It was evaluated on the same train/test records as the four-feature model from Experiment 2.

- **MAE:** 1.709,72 EUR, versus 426,98 EUR with four features
- **R²:** 0,8631, versus 0,9926 with four features
- **Non-positive predicted reference prices:** 9, flagged for manual review
- **Among 692 valid predictions:** 45 false acceptances and 139 false rejections

On the same 692 valid records, the four-feature model had **48 false acceptances**.

Although removing the quoted price slightly reduced false acceptances on these records, it substantially worsened reference-price prediction and increased false rejections.

This experiment highlights an important trade-off: the farmer's quoted price improves predictive accuracy, but a reference price derived partly from the farmer's own offer may not be sufficiently independent for evaluation purposes.

## Interpretation and Limitations

The current models are **not suitable for automatic price approval or rejection**.

Important limitations include:

- The prediction target is a **historical committee reference price**, not an independently verified regional market price.
- The experimental ±30% comparison is a **simplification** and does not reproduce the Evaluation Committee's full tiered decision procedure.
- False acceptances remain an important operational risk, even for models with strong regression metrics.
- The dataset lacks potentially important information, such as equipment condition, year of manufacture, standardized detailed specifications, and current market prices.
- Only 70 records are above 50.000 EUR, with 14 in the higher-priced test set.
- Evaluation uses a single train/test split; similar equipment may appear in both sets, potentially making performance estimates optimistic.
- The quoted-price input can make estimated reference prices dependent on the applicant's offer.

The results support further investigation of a **human-in-the-loop decision-support tool**.

The Evaluation Committee should retain responsibility for final decisions.

## How to Run the Notebooks

1. Open the notebooks in Google Colab, starting with `01_EDA.ipynb`.
2. Download `Mechanization_prices.xlsx` from the [GitHub repository](https://github.com/milatovicdanka90-maker/dsb_capstone) and upload it to the Colab session when needed. The EDA code expects the filename `Mechanization_prices.xlsx` in the current working directory.
3. Run the EDA notebook from top to bottom. It generates `prices_analysis_clean.csv`.
4. Open `02_modelling.ipynb` and run it from top to bottom. Modeling reads `Data/Processed/prices_analysis_clean.csv` directly from GitHub, so manual CSV upload is not needed.
5. If the cleaned EDA output is changed later, update the processed CSV on GitHub before rerunning Modeling so both notebooks use the same version.

**Note:** Google Colab sessions are temporary. The source Excel file may need to be uploaded again after restarting a session. Notebook filenames in the repository should be used as listed there if they differ in capitalization or spelling.

## Next Steps

Future work could:

- Improve equipment-name standardization.
- Add independent regional market prices.
- Test more robust train/test strategies.
- Assess false-acceptance risk more thoroughly.

Any deployment would require further validation and human oversight.

---

**Project status:** EDA, data preparation, three modeling experiments, error analysis, and initial evaluation are complete. The project is a research prototype, not a production decision system.
