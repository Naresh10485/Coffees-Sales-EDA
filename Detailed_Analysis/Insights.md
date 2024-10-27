
## Introduction
* This project aims to analyze the Coffee Sales data using Exploratory Data Analysis (EDA).
* The objective is to understand sales trends and identify patterns to guide decision-making.

## What is EDA?
* Exploratory Data Analysis (EDA) involves analyzing data sets to summarize their main characteristics.
* It often includes visualizations to uncover patterns, trends, and relationships.
* In this analysis, we explore the sales data of Coffee products.

## Library Used
This project utilizes various Python libraries for data analysis and visualization.
The libraries used are:
* **Pandas:** For data manipulation and analysis.
* **NumPy:** For numerical operations and array manipulation.
* **Matplotlib:** For creating static, animated, and interactive visualizations.
* **Seaborn:** For statistical data visualization.
* **Plotly Express:** For creating interactive plots.
The following code demonstrates how these libraries are imported into the Python environment:
```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

import plotly.express as px
```

## Data Overview
```python
Data = pd.read_csv('Coffee_Sales_Data.csv')
Data.columns
```

The dataset consists of coffee sales information including:
- date
- datetime
- cash_type
- card
- money
- coffee_name

The dataset spans from March 1st, 2024 to October 13th, 2024.

## Descriptive Statistics
* We have summarized key statistics like mean, median, mode, and standard deviation.
* Observed price ranges, total sales, and number of coffees.
* This step helped to understand the general structure of the data.

```python
Data.describe().T

Data.loc[:,['cash_type', 'card', 'coffee_name', ]].describe().T
```

## Data Type Conversion and New Features
* Convert object data type to datetime64[ns] using pd.to_datetime() which enables efficient time-based analysis by providing methods for extracting time components like months, weeks, and weekdays.
* Create new features e.g. 'time', 'month', 'week' and 'weekday'  for time-based analysis

```python
Data['date'] = pd.to_datetime(Data['date'])
Data['datetime'] = pd.to_datetime(Data['datetime'])
Data['time'] = Data['datetime'].dt.time
Data['month'] = Data['date'].dt.month_name()
Data['week'] =  Data['date'].dt.to_period('W')
Data['weekday'] = Data['date'].dt.day_name()
```

## Revenue Breakdown
* Extracted Revenue generated from each payment method e.g. cash and card and show their distribution using bar-chart.
```python
Total_money = Data['money'].sum()
card_money = Data[Data['cash_type'] == "card"]['money'].sum()
cash_money = Data[Data['cash_type'] == "cash"]['money'].sum()
payment_method =  pd.DataFrame({"cash_type": ['cash', 'card'], "money": [cash_money, card_money]})

plt.figure(figsize=(14,10))
fig = px.bar(payment_method, x="money", y="cash_type", title="Distribution of Payment Method",
             template= 'plotly_white', color='cash_type', text_auto= True)
fig.update_layout(xaxis_title="Money", yaxis_title="Cash Type", xaxis=dict(tickformat='.2f'))
fig.show()
```

* **Total Money:** 60501.64
* **Cash Money:**  3186.0
* **Card Money:**  57315.64

* Visualisation:
![Distribution of Payment method](/Visuals/Fig-methods.png)

