# Data Science Capstone: Agricultural Machinery Price Evaluation

## 1. Data Science Question

***Is a quoted agricultural machinery price within 30% of a reasonable regional market price?***

The goal of this project is to explore whether Data Science can help estimate realistic prices for new agricultural machinery.

I would like to investigate whether a model could help to predict or maybe automate machinery price evaluations and reduce the need for manual price checks by the Evaluation Committee.

## 2. Data Source

This project uses three datasets.

The first two datasets come from historical agricultural machinery evaluations and investment records of the Agency for Payments in Agriculture.

The third dataset contains agricultural machinery information from the USA, combined from four publicly available data tables.

The datasets include information about machinery prices, investment amounts, production sectors, and machinery characteristics.

## 3. Data Description

**Dataset 1: Agricultural Mechanization Prices**

File: `Mechanization_prices.xlsx`

- 6,622 rows and 12 columns.
- Contains information about machinery suppliers, equipment names, manufacturers, models, quoted prices, reference prices from the Evaluation Committee, and final evaluated prices.
- Includes price deviation levels of 10%, 15%, and 30% from the asking price.
- Some rows have missing information, so further data cleaning is needed.

**Dataset 2: Agricultural Investments**

File: `Mechanization.xlsx`

- 695 rows and 13 columns.
- Contains information about agricultural investments, total requested investment amounts, investment types, and production sectors.
- Includes 9 production sectors.
- Some records have missing information about the production sector.

**Dataset 3: USA Agricultural Machinery**

File: `Machinery_USA.csv`

- 438 rows and 14 columns.
- Contains information about machinery types, purchase prices in USD, working hours, useful life, and operating costs.
- An additional `production_sector` column was added to show which agricultural sectors each machine can be used in.
- Some machines can be used in more than one production sector. These sectors are separated by `|`.

### Relationship Between Datasets

The first dataset contains individual machinery items and their prices, while the second dataset contains information about agricultural investments.

I plan to use `groupby()` to calculate the total machinery investment amount for each investment number in the first dataset.

Then, I will try to connect these totals with the investment amounts in the second dataset using `merge()`. Small differences caused by rounding will need to be considered.

This would allow me to connect machinery prices with agricultural production sectors.

The third dataset contains additional machinery information from the USA.

I added the `production_sector` column to show which sectors the machines can be used in and to help compare different types of machinery.

However, the US dataset will first be analyzed separately because machinery prices in the USA may not be directly comparable with prices in Montenegro and the region.

## 4. Data Types

The three datasets contain numerical and categorical data.

**Dataset 1: Mechanization_prices**

- `float64`: 7 columns
- `object`: 5 columns

Most price columns are numerical. However, the reference price column also contains some text notes, so it needs further cleaning.

**Dataset 2: Mechanization**

- `int64`: 1 column
- `float64`: 3 columns
- `object`: 9 columns

The numerical columns contain investment amounts and record numbers, while the text columns contain information such as investment types and production sectors.

**Dataset 3: Machinery_USA**

- `float64`: 9 columns
- `object`: 5 columns

The numerical columns contain machinery prices, working hours, useful life, and operating costs.

The text columns contain information about equipment types, groups, sizes, cost basis, and production sectors.

There are no missing values in the USA dataset.

## 5. Problem Description

The main question of this project is whether Data Science can help determine if the prices of new agricultural machinery are realistic compared to market prices.

The Evaluation Committee researches market prices and compares them with the prices submitted by farmers.

The evaluation process uses price deviation levels of 10%, 15%, and 30%, based on the difference between the farmer's quoted price and the market price found by the Committee.

Depending on this difference, the Committee proposes an acceptable price, or the machinery item may be rejected if the price difference exceeds 30%.

This process takes time because market prices must be checked manually.

In this project, I want to explore whether Data Science can help estimate reasonable prices for new agricultural machinery using previous evaluations and other available data.

My long-term goal is to develop a tool that could reduce or possibly replace the need for Evaluation Committee checks for standard agricultural machinery.

The Committee would still be needed for construction works, special equipment, and other investments that cannot be easily compared with standard market products.

## 6. Potential Challenges

- **Data cleaning:** Some records contain missing values or text in columns that should contain numbers.
- **Combining datasets:** The datasets have different structures and need to be prepared before they can be connected.
- **Matching investment amounts:** Small differences caused by rounding may make it difficult to match records from the first two datasets.
- **Duplicate records:** Some machinery items appear more than once, so I need to check whether they are real duplicates or multiple items in the same investment.
- **Preparing data for modeling:** I will need to select the information that can help predict machinery prices.

## 7. Limitations

This project may have some limitations:

- **Historical data:** Machinery prices change over time, so older prices may not reflect current market prices.
- **Market differences:** Machinery prices in the USA may be different from prices in Montenegro and the region.
- **Missing information:** Some records do not contain all the technical details needed for accurate price comparison.
- **Comparable machinery:** There may not be enough similar machines in the datasets to predict prices accurately.
- **Model reliability:** The model may work better for common machinery than for less common or specialised equipment.

These limitations will be considered when evaluating the model and interpreting the results.

## 8. Model Evaluation

The target variable for this project will be `Evaluation Committee Reference Price (EUR)`.

The goal is to build a model that can predict a reasonable reference price for new agricultural machinery based on available machinery information.

I plan to evaluate the model using three regression metrics:

- **MAE (Mean Absolute Error):** Shows the average difference between predicted and actual reference prices.
- **RMSE (Root Mean Squared Error):** Helps identify larger prediction errors.
- **R² (R-squared):** Shows how well the model explains differences in machinery prices.

I will also compare the predicted reference prices with the prices submitted by farmers to see whether the model could help identify prices that are too high.

The results will help me understand whether the model could reduce the need for manual price evaluations by the Evaluation Committee.
