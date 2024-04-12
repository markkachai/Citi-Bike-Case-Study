# Citi Bike Case Study

## Table of Contents

- [Project Overview](#project-overview)
- [Data Sources](#data-sources)
- [Tools](#tools)
- [Data Cleaning](#data-cleaning)

### Project Overview
---
The objective of this final group project was to develop a tool to find the number of bikes to stock in a selection of stations at the beginning of the day to maximize the number of daily bike trips.

### Data Sources
---
Citi Bike Demand Data: The primary dataset used for this analysis is the "citibikeDemand.csv" file, which contains a random sample of trips taken from June 1, 2017, to May 31, 2018 (31,452 out of 15.7 million rides). The information has been supplemented with demographic, economic, and weather information pulled from a variety of sources. 

[Citi Bike Data Description](https://github.com/markkachai/Citi-Bike-Case-Study/blob/77f82f7f3303236dab7e363c86df7467ab18d046/CitiBike%20Data%20Description.docx)

### Tools
---
- Excel - Data Cleaning
  - [Download here](https://microsoft.com)
- RStudio - Data Analysis (EDA, Descriptive Analysis, Predictive Analysis)

### Data Cleaning
---
In the initial data preparation phase, we performed the following tasks:
1. Data loading and inspection.
2. Handling missing values.
3. Data cleaning and formatting.

### Exploratory Data Analysis
---
EDA involved exploring the Citi Bike demand data to answer any key questions, such as:

- Is demand higher during the daytime or evening?
- Which variables influence demand?
- How many bikes should we allocate to each station?
- How many trips between stations every day?

### Data Analysis
---
Examples of code worked with

- Finding average demand by time of day
```R
# Descriptive stats: DemandTime

citibike %>% group_by(DemandTime) %>% summarise(mean=mean(Demand), sd=sd(Demand))

## Average demand is higher in the evenings than in the mornings.
```

- One step further: finding average demand by time of day for each day of the week
```R
# Converting DayOfWeek to a factor with a specific order.

citibike$DayOfWeek <- factor(citibike$DayOfWeek, levels = c("Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday"))

# Descriptive stats: DemandTime, DayOfWeek

citibike %>% group_by(DemandTime, DayOfWeek) %>% summarise(mean=mean(Demand), sd=sd(Demand))

## Evening Surges and Steady Weekday Patterns: Demand for bikes increases with later times in the day and on average the amount of demand is the same
## throughout each day of the weekday and night. Demand slightly drops during the weekend.
```

- Visualizing our findings
```R
# Bar plot showing the average Demand for daytime and evening trips based on the day of the week.

avg_demand_time_day <- ggplot(citibike, aes(x = DayOfWeek , y = Demand, fill = DemandTime)) +
  geom_bar(stat = "summary", fun = "mean", position = "dodge") +
  geom_text(stat = "summary",
            fun = "mean",
            aes(label = sprintf("%.1f", after_stat(y)), group = DemandTime),
            position = position_dodge(width = 0.9),
            vjust = -0.5, 
            size = 3) +
  labs(title = "Average Demand for Daytime and Evening Trips During the Week",
       x = "Day of the Week",
       y = "Average Demand") +
  scale_fill_manual(values = c("daytime" = "lightblue", "evening" = "darkblue"), labels = c("Daytime", "Evening")) +
  theme_minimal()

print(avg_demand_time_day)
```
![Rplot](https://github.com/markkachai/Citi-Bike-Case-Study/assets/122057279/7b9df5c5-d3b7-46af-98d3-f28141f69146)

### Predictive Analysis

The predictive analysis involved creating a multiple linear regression model. The choice of variables to include in the model was crucial for accurately predicting bike sharing demand.

- Multiple linear regression model
```R
# Multiple Linear Regression Model:

reg1 <- lm(Demand ~ DemandTime + StartStationName + EndStationName + DayOfWeek + Month, data = citibike) 

summary(reg1)
```

- Example of predictions made for one of our five chosen stations on a particular day in the future (May 1, 2019)
```R
## Morning Demand
# predict using the predict function for Pershing Square North

predict(reg1, data.frame(DemandTime = "daytime", StartStationName = "Pershing Square North", EndStationName = "Pershing Square North", DayOfWeek = "Wednesday", Month = "5"))

predict(reg1, data.frame(DemandTime = "daytime", StartStationName = "Pershing Square North", EndStationName = "Broadway & E 22 St", DayOfWeek = "Wednesday", Month = "5"))

predict(reg1, data.frame(DemandTime = "daytime", StartStationName = "Pershing Square North", EndStationName = "W 21 St & 6 Ave", DayOfWeek = "Wednesday", Month = "5"))

predict(reg1, data.frame(DemandTime = "daytime", StartStationName = "Pershing Square North", EndStationName = "West St & Chambers St", DayOfWeek = "Wednesday", Month = "5"))

predict(reg1, data.frame(DemandTime = "daytime", StartStationName = "Pershing Square North", EndStationName = "W 41 St & 8 Ave", DayOfWeek = "Wednesday", Month = "5"))

## Evening Demand
# predict using the predict function for Pershing Square North

predict(reg1, data.frame(DemandTime = "evening", StartStationName = "Pershing Square North", EndStationName = "Pershing Square North", DayOfWeek = "Wednesday", Month = "5"))

predict(reg1, data.frame(DemandTime = "evening", StartStationName = "Pershing Square North", EndStationName = "Broadway & E 22 St", DayOfWeek = "Wednesday", Month = "5"))

predict(reg1, data.frame(DemandTime = "evening", StartStationName = "Pershing Square North", EndStationName = "W 21 St & 6 Ave", DayOfWeek = "Wednesday", Month = "5"))

predict(reg1, data.frame(DemandTime = "evening", StartStationName = "Pershing Square North", EndStationName = "West St & Chambers St", DayOfWeek = "Wednesday", Month = "5"))

predict(reg1, data.frame(DemandTime = "evening", StartStationName = "Pershing Square North", EndStationName = "W 41 St & 8 Ave", DayOfWeek = "Wednesday", Month = "5"))
```

### Optimization Model

The predictions for demand between our five chosen stations were then input into the parameters of our optimization model. Due to the high daytime/evening demand of our selected stations, we had to change the number of bikes from 1,000 to 4,000 to satisfy demand.

Using this optimization model allowed us to find:
- How many bikes should we allocate to each station?
  - Based on our model and the number of bikes, the demand forecast showed that the best initial allocation was 3,427 bikes.
- How many trips between stations every day?
  - Given our parameters, the demand forecast showed the number of trips to be 6,244.

### Results/Findings
---
The analysis results are summarized as follows:
1. R
2. R

### Proposed Solution and Business Model
---
Based on the analysis, we know evenings have the highest demand of 
- 

### Limitations
---
All null values were removed from the dataset, which only took out a small percentage of the data. This was to ensure null values were not being used in the analysis and also so the multiple linear regression model would work.

### References
---
1. [Stack Overflow](https://stackoverflow.com/)














