# Data Science Capstone: Agricultural Mechanization Price Evaluation

## 1. Data Science Question

**Is a quoted agricultural machinery price within 30% of a reasonable regional market price?**

The goal of this project is to explore whether Data Science can help estimate realistic prices for new agricultural mechanization.

I would like to investigate whether a machine learning model could help predict reference prices and support the evaluation process, reducing the need for manual price checks by the Evaluation Committee.

The project uses historical price data to compare farmers' quoted prices with reference prices.

The planned approach is to predict a reference price and then check whether the quoted price falls within a ±30% range.

## 2. Datasets

Two Excel datasets are used in this project:

| Dataset | Records | Columns | Description |
|---|---:|---:|---|
| `Mechanization_prices.xlsx` | 6.622 | 12 | Mechanization types, manufacturers, models, quoted prices, and reference prices |
| `Mechanization.xlsx` | 695 | 13 | Investment amounts, production sectors, municipalities, and investment descriptions |

The two datasets are analyzed separately because there is no confirmed common identifier to reliably connect them.

The `Mechanization_prices.xlsx` dataset is used for the main price analysis and planned machine learning model.

A Data Dictionary is included in the notebook to explain the columns in both datasets.

## 3. Tools and Libraries

- Python
- pandas
- NumPy
- Matplotlib
- Seaborn
- Google Colab
- scikit-learn (for future modeling)

## 4. Exploratory Data Analysis (EDA)

The first part of the analysis focuses on understanding the datasets.

The following steps were performed:

- Checking dataset shapes, data types, and basic statistics
- Identifying missing values as percentages of total records
- Checking duplicate records
- Exploring quoted and reference prices
- Analyzing price deviations
- Checking how many quoted prices fall within the ±30% threshold
- Visualizing reference price distributions
- Exploring investment amounts and production sectors
- Investigating unusually high investment amounts

### Missing Values

The mechanization prices dataset has around 40% missing values in several columns.

The `Manufacturer / Brand` column has the highest percentage, at 49,85%.

This may be related to the structure of the original Excel file, where many rows have incomplete mechanization information.

The investments dataset has very few missing values. The highest percentage is in `Primary Production Sector` (1,44%).

All three financial columns in the investments dataset have no missing values.

## 5. Data Cleaning and Preparation

### Mechanization Prices Dataset

The original dataset contains **6.622 records**.

After filtering records without essential information, **3.970 records** remained.

During data cleaning:

- 37 non-numeric evaluation notes were identified and preserved in a separate column.
- 3.520 records had numeric reference prices before removing duplicates.
- 450 records had no numeric reference price and were excluded from the main price analysis.
- 16 exact duplicate rows were identified and removed.
- A new column, `Record Frequency`, was created to preserve information about repeated records.
- Equipment names were reviewed, and one unclear description was corrected.
- No zero or negative quoted or numeric reference prices were found.

The duplicate analysis showed that:

- 3.941 records appeared once.
- 10 records appeared twice.
- 3 records appeared three times.

After removing duplicates, `prices_clean` contains **3.954 records**.

The final dataset used for price analysis, `prices_analysis`, contains **3.505 records** with both quoted and reference prices.

### Mechanization Investments Dataset

During EDA, two unusually high investment amounts were identified:

| Record No. | Investment Amount |
|---|---:|
| 677 | 4.035.392,00 EUR |
| 678 | 7.617.603,00 EUR |

These amounts were also present in the original Excel file, but the correct values could not be confirmed.

The two records were excluded from the cleaned investment dataset, while the original data remained unchanged.

After cleaning, `investments_clean` contains **693 records**.

The investment amount distribution was visualized again. Most investments have relatively low values, while a smaller number have much higher amounts.

The distribution remains right-skewed.

### Investment Tiers

The investment amounts were divided into two groups using **100.000 EUR** as a threshold.

| Investment Tier | Records | Percentage | Average Amount |
|---|---:|---:|---:|
| Lower Investments (up to 100.000 EUR) | 535 | 77,2% | 47.435,57 EUR |
| Higher Investments (above 100.000 EUR) | 158 | 22,8% | 280.357,01 EUR |

This division helps compare smaller and larger investments separately.

A second concentration of investments appears near **650.000 EUR**.

The public call defined an investment range from 10.000 to 650.000 EUR, excluding VAT. This may explain the concentration near the upper limit.

The threshold of 100.000 EUR was selected as a practical division for analysis, rather than a statistically confirmed boundary between two groups.

## 6. Main Findings

The price deviation was calculated using the following formula:

**Price Deviation (%) = (Quoted Price − Reference Price) / Reference Price × 100**

The calculated deviations were compared with the original deviation values, after converting them to percentages.

The results matched almost exactly for 3.500 records. Five records had missing original deviation values.

Among the **3.505 records** with both quoted and reference prices:

| Price Evaluation | Records | Percentage |
|---|---:|---:|
| Within ±30% of the reference price | 2.893 | 82,5% |
| Outside ±30% of the reference price | 612 | 17,5% |

Most quoted prices fall within the selected threshold.

However, some prices show much larger positive deviations, with the highest reaching almost 400%.

Reference prices are also right-skewed, with many lower-priced items and a smaller number of expensive machines.

These results describe historical price differences and do not represent machine learning predictions.

## 7. Feature Engineering

Four features were selected for future machine learning modeling:

| Feature | Description |
|---|---|
| `Equipment / Item` | Type of mechanization |
| `Manufacturer / Brand` | Manufacturer |
| `Applicant Quoted Price (EUR)` | Farmer's quoted price |
| `Model / Specifications` | Model and technical specifications |

The target variable (`y`) is:

**`Evaluation Committee Reference Price (EUR)`**

Missing values in the manufacturer and model specifications columns were replaced with `Unknown`.

After preparation, there are no missing values in the four selected features.

The text features still need to be transformed into numerical representations before model training.

## 8. Next Steps

The next stage of the project will focus on machine learning.

The planned steps are:

1. Split the dataset into training and test sets while taking investment groups into account.
2. Encode categorical features and transform text specifications.
3. Build a baseline regression model to predict reference prices.
4. Evaluate model performance using appropriate regression metrics.
5. Compare predicted reference prices with farmers' quoted prices.
6. Check whether quoted prices fall within the ±30% threshold.

The final goal is to explore whether machine learning can support more consistent and efficient mechanization price evaluations.

## 9. Project Files

- `01_EDA.ipynb` – Exploratory Data Analysis, data cleaning, and feature preparation
- `Mechanization_prices.xlsx` – Historical mechanization price data
- `Mechanization.xlsx` – Investment information
- `README.md` – Project description and current results

**Project status:** EDA, data cleaning, and initial feature preparation have been completed. Machine learning modeling and evaluation are the next stages.
