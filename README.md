# Citi Bike Case Study

## Table of Contents

- [Project Overview](#project-overview)
- [Data Sources](#data-sources)
- [Tools](#tools)
- [Data Cleaning](#data-cleaning)
- [Exploratory Data Analysis](#exploratory-data-analysis)
- [Data Analysis](#data-analysis)
- [Predictive Analysis](#predictive-analysis)
- [Prescriptive Analysis and Optimization Model](#prescriptive-analysis-and-optimization-model)
- [Results/Findings](#results/findings)
- [Proposed Solution and Business Model](#proposed-solution-and-business-model)
- [Limitations](#limitations)
- [References](#references)

### Project Overview
---
The objective of this final group project was to develop a tool to find the number of bikes to stock in a selection of stations at the beginning of the day to maximize the number of daily bike trips. Applying the 4-step analytics process to solve this problem, our group constructed a data-driven narrative prepared in a pitch presentation for the Citi Bike executives. In RStudio, descriptive analyses helped to identify demand patterns, and predictive analyses were done through the use of a multiple linear regression model created to forecast daytime and evening demand for five stations. An optimization model created in Excel was used to find the optimal initial allocation of bikes as well as the number of trips.

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
---
Here's an explanation of why each predictor/variable was chosen:

DemandTime:

The time of day (e.g., morning, evening) can significantly impact bike sharing demand, as we’ve seen there is a higher average demand in the evenings across days of the week.

StartStationName and EndStationName:

The starting and ending stations are fundamental variables as they directly relate to the origin and destination of bike trips. Different stations may have varying levels of demand based on factors such as location, nearby attractions, population density, and transportation options.

DayOfWeek:

The day of the week affects commuting patterns, leisure activities, and overall demand. For example, weekdays typically exhibit higher demand during peak commuting hours, while weekends may see more leisure-oriented trips.

Month:

Seasonal variations can have a significant impact on bike-sharing demand. Weather conditions, holidays, and cultural events may influence the number of people using bike-sharing services. For instance, demand may increase during warmer months or decrease during holiday periods.

By including these variables in our model, we can capture a comprehensive set of factors influencing bike-sharing demand. This allows the model to account for variations in demand based on our chosen predictors, leading to more accurate predictions.

The multiple R^2 represents the proportion of variance explained by all the predictors together. In our model, the multiple R^2 is 0.8956, indicating that we can explain approximately 89.56% of the variance in our data with this model.

The p-value for F-statistics is 2.2*10^-16, which is < 0.05, indicating that the regression model is highly significant, and the predictors collectively have a significant effect on predicting bike demand.  With this p-value, we know at least one of the coefficients is non-zero, and the model is valid.

Additionally, considering these variables enables you to analyze and understand the underlying factors driving bike-sharing demand, which is essential for optimizing resource allocation and planning.

---

This multiple linear regression model allows us to predict daytime and evening demand on a particular day in the future for our chosen stations:

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

### Prescriptive Analysis and Optimization Model
---
The predictions for demand between our five chosen stations were then input into the parameters of our optimization model. Due to the high daytime/evening demand of our selected stations, we had to change the number of bikes from 1,000 to 4,000 to satisfy demand.

Using this optimization model allowed us to find:
- How many bikes should we allocate to each station?
  - Based on our model and the number of bikes, the demand forecast showed that the best initial allocation was 3,427 bikes.
- How many trips between stations every day?
  - Given our parameters, the demand forecast showed the number of trips to be 6,244.

### Results/Findings
---
The analysis results are summarized as follows:
1. On average, evenings have a higher demand than mornings.
2. On average, demand slightly decreases during the weekend for both times of day.
3. Our multiple linear regression model explains 89.56% of the variance in our data.
4. Given our five stations, the best initial allocation of bikes is 3,427 bikes.

### Proposed Solution and Business Model
---
Based on the analysis, we recommend the following actions:
- Increase the initial allocation of bikes to 3,427.
- Provide cashback or discount opportunities to riders/people close to stations to displace extra bikes to the nearest high-demand stations.
- Allow commuters/students to earn extra money/reward for displacing bikes from one station to another suggested station (Ex. Earn a reward per 5 bikes displaced).
- A designated app for Citi Bike could manage and display all the displacement suggestions, discounts, cashback, and student account information.

### Limitations
---
All null values were removed from the dataset, which only took out a small percentage of the data. This was to ensure null values were not being used in the analysis and also so the multiple linear regression model would work.

### References
---
1. [Stack Overflow](https://stackoverflow.com/)
