# Online Shopping Purchase Intention: Data Science Capstone

## 1. Data Question

**Which visitor characteristics and browsing behaviors are associated with online purchase conversion?**

The goal is to explore which visitor characteristics and browsing behaviors are related to her a website session ends with a purchase.

## 2. Data Source

The dataset used in this project is the **Online Shoppers Purchasing Intention Dataset**, available from the UCI Machine Learning Repository.

Source: [https://archive.ics.uci.edu/dataset/468/online+shoppers+purchasing+intention+dataset](https://archive.ics.uci.edu/dataset/468/online+shoppers+purchasing+intention+dataset)

The dataset contains 12.330 website sessions and 18 columns. It includes information about visitor types, browsing activity, time spent on different pages, traffic sources, and whether a purchase was made. The target variable is `Revenue`, which indicates whether a session resulted in a purchase.

## 3. Problem Description

Not every website visit results in a purchase. Understanding which visitor characteristics and browsing behaviors are associated with purchases can help us better understand online shopping patterns.

In this project, we will explore the dataset and identify patterns that may help explain the difference between sessions with and without a purchase. Later, we plan to use classification models to predict whether a website session will result in a purchase.

## 4. Potential Challenges

- **Class imbalance:** There are many more sessions without a purchase than sessions with a purchase.
- **Duplicate rows:** The dataset contains 125 duplicate rows, which need to be reviewed before modeling.
- **Different data types:** The dataset includes numerical and categorical variables.
- **Interpretation of results:** A relationship between a variable and a purchase does not necessarily mean that the variable causes the purchase.
- **Model evaluation:** Accuracy alone may not be enough to evaluate model performance because the target classes are imbalanced.

## 5. Work Completed So Far

- Loaded the dataset into a pandas DataFrame named `osi`.
- Inspected the dataset using `info()` and `head()`.
- Checked the dataset dimensions using `shape`.
- Reviewed descriptive statistics using `describe().T`.
- Checked for missing values using `isnull().sum()`.
- Checked for duplicate rows using `duplicated().sum()` and found 125 duplicates.
- Explored the distribution of the target variable `Revenue` using `value_counts()` and a bar chart.
- Compared average `ProductRelated`, `ProductRelated_Duration`, `BounceRates`, `ExitRates`, `PageValues`, and `SpecialDay` values between sessions with and without a purchase using `groupby()` and `mean()`.
- Examined purchase counts by `VisitorType`.
- Visualized the distribution of sessions by `VisitorType`.
- Examined the number of sessions by `Month`.
- Compared purchase and non-purchase counts across months.
