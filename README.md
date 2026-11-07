# Data Science Capstone: Agricultural Machinery Price Evaluation

## 1. Data science question

_Is a quoted agricultural machinery price within 30% of a reasonable regional market price?_

The goal of this project is to explore whether Data Science can help estimate realistic prices for new agricultural machinery.

I would like to investigate whether a model could help automate machinery price evaluations and reduce the need for manual price checks by the Evaluation Committee.

2. Data source

This project uses three datasets.

Dataset 1: Agricultural Machinery Prices

File: Mechanization_prices_EN.xlsx

This dataset contains historical machinery price evaluations from the Agency for Payments in Agriculture.

It includes information about machinery suppliers, machinery types, models, prices submitted by applicants, prices found by the Evaluation Committee, and final evaluated prices.

Dataset 2: Agricultural Investments

File: Mechanization_English.xlsx

This dataset contains information about agricultural investments, including total investment amounts and production sectors.

I plan to connect this dataset with the first dataset by grouping machinery items by investment number and comparing their total values.

Dataset 3: Agricultural Machinery — USA

File: machinery_all_with_production_sector.csv

This dataset contains 438 records of agricultural machinery from four US data tables.

It includes machinery types, purchase prices in USD, working hours, useful life, and operating costs.

I also added a production_sector column to show which agricultural sectors each machine can be used in. Some machines can be used in more than one sector.

The US data will be used for additional analysis. I will also check whether these prices can be compared with prices in Montenegro and the region.

3. Problem description

I work at the Agency for Payments in Agriculture, where one of our tasks is to check whether the prices of agricultural machinery submitted by applicants are realistic.

Currently, an Evaluation Committee researches market prices and compares them with the prices submitted by applicants.

The Agency uses price deviation levels of 10%, 15%, and 30% when evaluating machinery prices.

Depending on the difference between the submitted price and the reference price, the proposed price may be accepted, reduced, or rejected.

This process takes time because market prices must be checked manually.

In this project, I want to explore whether Data Science can help estimate reasonable prices for new agricultural machinery using previous evaluations and other available data.

My long-term goal is to develop a tool that could reduce or possibly replace the need for Evaluation Committee checks for standard agricultural machinery.

The Committee would still be needed for construction works, special equipment, and other investments that cannot be easily compared with standard market products.

4. Potential challenges

Combining datasets: The three datasets contain different information and need to be prepared before they can be used together.

Matching investment amounts: Small differences caused by rounding may make it difficult to connect records from the first two datasets.

Missing or incorrect data: Some records may contain missing information or values that need to be checked.

Machinery types: Different machines have different characteristics and prices, which may make price prediction more difficult.

Production sectors: Some machines, such as tractors, can be used in several agricultural sectors.

Different markets: The US dataset contains prices in dollars, while the Agency's data contains prices in euros. Market differences also need to be considered.

Model accuracy: The model may not always predict the correct market price, especially when there are not enough similar machines in the dataset.

5. Work completed so far

Defined the main Data Science question and project goal.

Selected three datasets for the project.

Reviewed the structure and columns of the datasets.

Prepared English versions of the Agency's datasets.

Checked the number of rows and columns in the datasets.

Reviewed missing values and duplicate records in the US dataset.

Combined four US machinery tables into one dataset containing 438 records.

Added a production_sector column to the US dataset.

Identified possible problems when connecting investment amounts from the first two datasets.

Planned to use groupby() to calculate total investment amounts.

Planned to use merge() to connect the datasets.

The next step is to continue working in Python using pandas. I will clean and explore the data, try to connect the datasets, and later test whether a model can predict reasonable machinery prices.
