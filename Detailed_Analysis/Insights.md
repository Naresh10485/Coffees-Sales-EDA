
## Introduction
* This project aims to analyze the Coffee Sales data using Exploratory Data Analysis (EDA).
* The objective is to understand sales trends and identify patterns to guide decision-making.

## What_is_EDA?
* Exploratory Data Analysis (EDA) involves analyzing data sets to summarize their main characteristics.
* It often includes visualizations to uncover patterns, trends, and relationships.
* In this analysis, we explore the sales data of Coffee products.

## Library_Used
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

## Data_Overview
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

## Descriptive_Statistics
* We have summarized key statistics like mean, median, mode, and standard deviation.
* Observed price ranges, total sales, and number of coffees.
* This step helped to understand the general structure of the data.

```python
Data.describe().T

Data.loc[:,['cash_type', 'card', 'coffee_name', ]].describe().T
```

## Data_Type_Conversion_and_New_Features
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

## Revenue_Breakdown
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

## Summarize_Data_with_Pivot
 This table summarizes the data by total sales revenue across every month week day sales.

 ```python
 pivot = pd.crosstab(index= Data['weekday'], columns= Data['month'], values=Data['money'], aggfunc=sum)
pivot.style.background_gradient()
```
* Visualisation:
![Pivot Table](/Visuals/Pivot.png)

* This pivot table helps to highlights the peak sales days, revealing that Tuesday, Sunday, and Monday generate the highest sales volume.
```python
pivot.sum(axis=1).sort_values(ascending= False).plot(kind ='bar')
plt.xticks(rotation= 15)
plt.show()
```
![weekday sales](/Visuals/sales_weeks.png)
<bt>
![Sales Performance in a month](/Visuals/sales_performance.png)

## Monthly_Sales
* To visualize monthly sales trends, we grouped the data by month.
```python
Data_monthly = Data.groupby(by = 'month').agg({"money": ["sum", "count"]}).reset_index()
Data_monthly.columns = ["month", "money", "cups"]
Data_monthly['month_number'] = [4,8,7,6,3,5,10,9]
Data_monthly.sort_values(by = 'month_number', inplace= True)
Data_monthly.drop(columns= 'month_number', inplace= True)
Data_monthly.reset_index(inplace= True, drop= True)
Data_monthly
```
* By visualizing monthly sales data as a bar chart, we have easily identified sales trends and pinpoint peak sales months.
```python
fig = px.bar(Data_monthly, x = 'month', y = 'money', color='month', template='plotly_white', text_auto= True,
             title= "Total Revenue generated from Coffee Sales, April-October(2024)")
fig.update_layout(
    xaxis_title="Month",
    yaxis_title="Revenue",
    yaxis=dict(tickformat=".2f")
)
fig.show()
```
* **Maximum Sales Month:**  September (Rs. 9988.64), May, June

* Visualisation:
![Monthly Revenue](/Visuals/Monthly%20Sales.png)

## Weekly_Sales:
* We've visualized weekly sales trends to quickly spot the best and worst performing weeks of each month.
```python
Data_weekly = Data.groupby(by = 'week').agg({"money": ["sum", "count"]}).reset_index()
Data_weekly.columns = ["week", "money", "cups"]
Data_weekly
```

```python
week_per_month = Data.groupby(by = ['month','week']).agg({'money':['sum','count']}).reset_index()
week_per_month.columns = ['month','week','money','cups']
week_per_month
```
* Visualisation:
```python
fig = px.bar(week_per_month.sort_values(["week"]), x="month", y="money", color="week", title="Total Revenue generated from Coffee Sales, April-October(2024) in every week", template= 'plotly_white')
fig.update_layout(xaxis_title="Month", yaxis_title="Revenue", yaxis=dict(tickformat=".2f"),showlegend= False)
fig.show()
```
![Weekly sales](/Visuals/Weekly%20Trend.png)

#### Insights
* **Maximum Sales week:**  2024-10-07/2024-10-13
* **Minumum Sales week:**  2024-02-26/2024-03-03

## Sales Trend Line across month

```python
fig = px.line(Data_monthly, x="month", y="money", title="Sales Trend Over year", template='plotly_white')
fig.update_layout(xaxis_title="Month", yaxis_title="Sales Revenue", yaxis = dict(tickformat = '.2f'))
fig.show()
```

* Visualisation:
![Time series line plots showing monthly sales trends](/Visuals/Sales%20trend.png)

## Coffee_Prices_distribution
* We've used box plots to analyze the variability in coffee prices over time, helping us understand how price changes have affected sales.
```python 
fig = px.box(Data, x="coffee_name", y="money", title="Coffee Prices distribution", template= 'plotly_white')
fig.update_layout(xaxis_title="Coffee Name", yaxis_title="Price")
fig.show()
```
* Visualisation:
![Coffee Prices Distribution](/Visuals/Boxplot.png)

## Coffee_Preferences
* To Visuals the Customer Choices for particular coffee, We have grouped the Coffee sales by Coffee-name and try to visualise their sales using bar chart.

```python
coffee_sales = Data.groupby(by= 'coffee_name').agg({"money": ["sum", "count"]}).reset_index()
coffee_sales.columns = ['coffee_name', 'money', 'cups']
coffee_sales_sorted = coffee_sales.sort_values(by = 'money', ascending= False, ignore_index= True)
coffee_sales_sorted
```
* bar chart Visualisation
```python
plt.figure(figsize = (12,6))
fig=px.bar(data_frame= coffee_sales_sorted, x = 'coffee_name', y = 'money', title = 'Customer Preference for Coffee',color= 'coffee_name',template= 'plotly_white', color_discrete_sequence=px.colors.sequential.Viridis
      )
fig.show()
```

![Coffee Preference](/Visuals/Coffee%20Preference.png)

#### Insights
* There is maximum sales for Latte, Americano with Milk indicating that customer prefered more these coffees.
* There is minimum sales for Cocoa, Espresso.

## Sales_During_Day_Time
* To visuals the Sales during day time, we grouped the Data into Morning, Evening and Afternoon and try to visuals Coffee Sales during day time.
* This Chart indicate that there is maximum sales in the morning compared to Afternoon and Evening.
* It can be easily seen that there is no pattern for customer to prefer any coffee during morning or evening.

```python
Data['time_of_day'] = pd.cut(Data['datetime'].dt.hour,
                             bins=[0, 6, 12, 17, 24],
                             labels=['Night', 'Morning', 'Afternoon', 'Evening'])


grouped_data = Data.groupby(by = ['time_of_day', 'coffee_name']).agg({"money": ["sum", "count"]}).reset_index()
grouped_data.columns = ['time_of_day', 'coffee_name', 'money', 'cups']


fig = px.bar(grouped_data, x='time_of_day', y='money', color='coffee_name', barmode='group', title = 'Coffee Sales Distribution by Time of Day', template= 'plotly_white')
fig.show()
```
* Visualisation:
![Coffee Sales During day time](/Visuals/Day%20time.png)

## Key_Insights
* **Card Payments Dominate:** Card payments were the preferred method for most customers.
* **September Sales Peak:** The highest revenue was generated in the month of September.
* **Latte and Americano Lead:** Latte and Americano with milk were the most popular coffee choices.
* **Morning Rush:** The majority of sales occurred during the morning hours.

## Recommendations
* **Leverage September Success:** Analyze September's high-performing strategies to replicate them during other months to boost overall sales.
* **Focus on Popular Coffees:** Prioritize the supply and promotion of Latte and Americano with milk to cater to customer preferences.
* **Capitalize on Morning Rush:** Implement strategies to attract more customers during peak morning hours, such as early bird discounts or loyalty programs.
* **Customer Feedback:** Actively seek customer feedback to understand their preferences and tailor offerings accordingly.

## Conclusions
* EDA provided valuable insights into coffee sales trends and patterns.
* These insights can guide data-driven decisions to improve sales performance.
* Further analysis with customer demographics could offer deeper insights.

# Thank You
## Naresh Kumar
