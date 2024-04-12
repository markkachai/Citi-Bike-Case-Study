# Citi-Bike-Case-Study

### Project Overview

The objective of this final group project was to develop a tool to find the number of bikes to stock in a selection of stations at the beginning of the day to maximize the number of daily bike trips.

### Data Sources

Citi Bike Demand Data: The primary dataset used for this analysis is the "citibikeDemand.csv" file, which contains a random sample of trips taken from June 1, 2017, to May 31, 2018 (31,452 out of 15.7 million rides). The information has been supplemented with demographic, economic, and weather information pulled from a variety of sources. 

Citi Bike Data Description

### Tools

- Excel - Data Cleaning
  - [Download here](https://microsoft.com)
- RStudio - Data Analysis (EDA, Descriptive Analysis, Predictive Analysis)

### Data Cleaning

In the initial data preparation phase, we performed the following tasks:
1. Data loading and inspection.
2. Handling missing values.
3. Data cleaning and formatting.

### Exploratory Data Analysis

EDA involved exploring the Citi Bike demand data to answer any key questions, such as:

- Is demand higher during the daytime or evening?
- Which variables influence demand?
- How many bikes should we allocate to each station?
- How many trips between stations every day?

### Data Analysis

Examples of code worked with


```R
# Descriptive stats: DemandTime

citibike %>% group_by(DemandTime) %>% summarise(mean=mean(Demand), sd=sd(Demand))

## Average demand is higher in the evenings than in the mornings.
```












