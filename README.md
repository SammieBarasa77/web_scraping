# Data Project: Web Scraping using BeautifulSoup
![Logo](https://github.com/SammieBarasa77/web_scraping/blob/main/assets/images/original_2.png)

## Introduction
In this project, I leveraged BeautifulSoup to perform web scraping and extract comprehensive data on the top companies in the United States. The goal was to gather valuable insights into industry trends, company rankings, and key performance metrics. This project showcases my ability to automate data collection, clean and organize datasets, and derive business-critical insights, demonstrating the potential for data-driven decision-making in competitive markets.

# Web Scraping Project: Top US Companies by Revenue

This project demonstrates the use of **BeautifulSoup** for web scraping to extract data on the top companies in the United States from Wikipedia. The extracted data focuses on the largest private companies by revenue (Table 2) and is further cleaned, analyzed, and saved for business insights.

---

## Table of Contents
1. [Introduction](#introduction)  
2. [Objective](#objective)  
3. [Technologies Used](#technologies-used)  
4. [Dataset Source](#dataset-source)  
5. [Procedure](#procedure)  
    - [Step 1: Import Necessary Libraries](#step-1-import-necessary-libraries)  
    - [Step 2: Fetch the Web Page Content](#step-2-fetch-the-web-page-content)  
    - [Step 3: Parse the HTML Content](#step-3-parse-the-html-content)  
    - [Step 4: Locate the Desired Table](#step-4-locate-the-desired-table)  
    - [Step 5: Extract Data from the Table](#step-5-extract-data-from-the-table)  
    - [Step 6: Convert to DataFrame](#step-6-convert-to-dataframe)  
    - [Step 7: Clean the Data](#step-7-clean-the-data)  
    - [Step 8: Save the Data](#step-8-save-the-data)  
6. [Results](#results)  
7. [Insights and Observations](#insights-and-observations)  
8. [Future Work](#future-work)  
9. [Acknowledgments](#acknowledgments)

---

## Introduction
This project involves web scraping data on the **largest private companies by revenue** in the United States using BeautifulSoup. It demonstrates web scraping, data extraction, cleaning, and saving the data for further analysis.

## Objective
To extract and analyze data from Wikipedia, focusing on private companies' revenue, and to showcase web scraping skills and data handling for business insights.

## Technologies Used
- Python  
- BeautifulSoup  
- pandas  
- requests  

```python
import requests
from bs4 import BeautifulSoup
import pandas as pd
```

## Dataset Source
The dataset was scraped from the [Wikipedia page](https://en.wikipedia.org/wiki/List_of_largest_companies_in_the_United_States_by_revenue).

## Procedure

### Step 1: Import Necessary Libraries
Import libraries like `requests`, `BeautifulSoup`, and `pandas` to handle HTTP requests, parse HTML, and structure data.

### Step 2: Fetch the Web Page Content
Use the `requests` library to fetch the HTML content of the webpage.

```python
url = "https://en.wikipedia.org/wiki/List_of_largest_companies_in_the_United_States_by_revenue"
page = requests.get(url)
soup = BeautifulSoup(page.text, 'html')
print(soup)
```

### Step 3: Parse the HTML Content

Finding all tables availabe on the webpage for easier HTML structure navigation.

```python
soup.find_all('table')
```
### Step 4: Locate the Desired Table
Identify and locate **Table 2** containing the data on the largest private companies by revenue.

```python
#Finding the first table (List of companies)from the page using indexing 
soup.find_all('table')[0]
```
Finding the desired table
```python
table = soup.find_all('table')[0]
```

### Step 5: Extract Data from the Table
Extract table rows, headers, and cell data 

Printing table headlines
```python
world_titles = table.find_all('th')
```
Printing titles
```python
print(world_titles)
```
Table titles in a row
```python
world_table_titles = [title.text.strip() for title in world_titles]
print(world_table_titles )
```

### Step 6: Convert to DataFrame
Convert the extracted data into a structured pandas DataFrame.

Titles
```python
df = pd.DataFrame(columns = world_table_titles)
df
```
Whole data into DataFrame
```python
column_data = table.find_all('tr')

for row in column_data:
    row_data = row.find_all('td')
    individual_row_data = [data.text.strip() for data in row_data]
    print(individual_row_data)
```
### Step 7: Clean the Data
Perform data cleaning steps such as removing unwanted characters, handling missing values, and formatting data.
```python
# Convert revenue column to numeric 
df['Revenue'] = df['Revenue'].str.replace(r'[^\d.]', '', regex=True).astype(float)

# Replace missing values
df.fillna('N/A', inplace=True)

# Remove commas and convert the 'Revenue (USD millions)' column to numeric
df['Revenue (USD millions)'] = df['Revenue (USD millions)'].replace({',': ''}, regex=True).astype(float)

# Now, perform the revenue analysis by industry
industry_revenue = df.groupby('Industry')['Revenue (USD millions)'].agg(['mean', 'sum']).sort_values(by='sum', ascending=False)

print("\nRevenue by Industry (Average and Total):")
print(industry_revenue)
```
The Data
```python
for row in column_data[1:]:
    row_data = row.find_all('td')
    individual_row_data = [data.text.strip() for data in row_data]

    length = len(df)
    df.loc[length] = individual_row_data
```
### Step 8: Save the Data
Save the cleaned DataFrame to a CSV file for further analysis.
```python
df.to_csv(r'C:\Users\samue\Documents\Web Scrapper\Top_Companies.csv', index=False)
```

## Exploratory Data Analysis
Setting up Seaborn style for cleaner visuals
```python
import matplotlib.pyplot as plt
import seaborn as sns

# Setting up Seaborn style for cleaner visuals
sns.set(style="whitegrid")

```
Data Overview
```python
# Summary Statistics
print("Summary Statistics:")
print(df.describe())

# Check Data Types
print("\nData Types:")
print(df.dtypes)

# Missing Values
print("\nMissing Values:")
print(df.isnull().sum())
```
Revenue Analysis
```python
# Top Companies by Revenue
top_revenue_companies = df.sort_values(by='Revenue (USD millions)', ascending=False).head(10)
print("\nTop 10 Companies by Revenue:")
print(top_revenue_companies[['Rank', 'Name', 'Revenue (USD millions)']])

# Revenue Distribution (Histogram and Box Plot)
plt.figure(figsize=(14, 6))

plt.subplot(1, 2, 1)
sns.histplot(df['Revenue (USD millions)'], kde=True)
plt.title('Revenue Distribution')

plt.subplot(1, 2, 2)
sns.boxplot(x=df['Revenue (USD millions)'])
plt.title('Revenue Box Plot')

plt.show()
```
Industry Revenue
```python
# Plot Revenue by Industry
plt.figure(figsize=(10, 6))
sns.barplot(data=industry_revenue.reset_index(), x='sum', y='Industry')
plt.title('Total Revenue by Industry')
plt.xlabel('Total Revenue in Millions (USD)')
plt.show()
```
Employee Analysis
```python
# Employee Distribution (Histogram)
plt.figure(figsize=(8, 6))
sns.histplot(df['Employees'], kde=True)
plt.title('Employee Count Distribution')
plt.xlabel('Number of Employees')
plt.show()
```
Employee Density
```python
# Remove commas and convert the 'Employees' column to numeric, coercing errors to NaN
df['Employees'] = df['Employees'].replace(r'\D+', '', regex=True).astype(float)

# Optionally, drop rows where 'Employees' is NaN if you want to exclude them
df = df.dropna(subset=['Employees'])

# Employee Density Per industry 
industry_employee_density = df.groupby('Industry')['Employees'].mean().sort_values()
plt.figure(figsize=(10, 6))
sns.barplot(x=industry_employee_density, y=industry_employee_density.index)
plt.xlabel('Average Number of Employees')
plt.ylabel('Industry')
plt.title('Employee Density by Industry')
plt.show()
```
Revenue Per Employee
```python
# Revenue per Employee
df['Revenue_per_Employee'] = df['Revenue (USD millions)'] / df['Employees']
plt.figure(figsize=(8, 6))
sns.histplot(df['Revenue_per_Employee'], kde=True)
plt.title('Revenue per Employee Distribution')
plt.xlabel('Revenue per Employee (USD)')
plt.show()
```
Industry Analysis
```python
# Industry Popularity (Number of Companies per Industry)
industry_popularity = df['Industry'].value_counts()
plt.figure(figsize=(10, 6))
sns.barplot(y=industry_popularity.index, x=industry_popularity.values)
plt.title('Number of Companies per Industry')
plt.xlabel('Number of Companies')
plt.show()
```

```python
# Revenue and Employees by Industry
industry_stats = df.groupby('Industry').agg({'Revenue (USD millions)': 'sum', 'Employees': 'sum'}).sort_values(by='Revenue (USD millions)', ascending=False)
industry_stats.plot(kind='bar', figsize=(14, 6), secondary_y='Employees', title='Total Revenue and Employee Count by Industry')
plt.show()

# Revenue Distribution per Industry (Box Plot)
plt.figure(figsize=(14, 8))
sns.boxplot(x='Revenue (USD millions)', y='Industry', data=df)
plt.title('Revenue Distribution by Industry')
plt.show()

```
Geographical Analysis
```python
# Headquarters Analysis
headquarters_count = df['Headquarters'].value_counts().head(10)  # Top 10 locations
plt.figure(figsize=(10, 6))
sns.barplot(x=headquarters_count.values, y=headquarters_count.index)
plt.title('Top Headquarters Locations')
plt.xlabel('Number of Companies')
plt.show()

# Revenue by Location (State or City)
df['State'] = df['Headquarters'].apply(lambda x: x.split(',')[-1].strip())  # Extracting state if format is "City, State"
revenue_by_state = df.groupby('State')['Revenue (USD millions)'].sum().sort_values(ascending=False).head(10)
plt.figure(figsize=(10, 6))
sns.barplot(x=revenue_by_state.values, y=revenue_by_state.index)
plt.title('Top 10 States by Total Revenue')
plt.xlabel('Total Revenue (USD)')
plt.show()

# Top Cities for Employment
employment_by_state = df.groupby('State')['Employees'].sum().sort_values(ascending=False).head(10)
plt.figure(figsize=(10, 6))
sns.barplot(x=employment_by_state.values, y=employment_by_state.index)
plt.title('Top 10 States by Total Employees')
plt.xlabel('Total Employees')
plt.show()
```
Rank Vs Finances
```python
# Rank vs. Revenue
plt.figure(figsize=(8, 6))
sns.scatterplot(x='Rank', y='Revenue (USD millions)', data=df)
plt.title('Rank vs Revenue')
plt.xlabel('Rank')
plt.ylabel('Revenue in Millions (USD)')
plt.show()

# Rank vs. Employees
plt.figure(figsize=(8, 6))
sns.scatterplot(x='Rank', y='Employees', data=df)
plt.title('Rank vs Employees')
plt.xlabel('Rank')
plt.ylabel('Number of Employees')
plt.show()
```

Comparing key metrics
```python
# Revenue and Employee Correlation
plt.figure(figsize=(8, 6))
sns.scatterplot(x='Employees', y='Revenue (USD millions)', data=df)
plt.title('Employees vs Revenue')
plt.xlabel('Number of Employees')
plt.ylabel('Revenue (USD)')
plt.show()

# Distribution of Top Metrics (Violin Plot for Revenue)
plt.figure(figsize=(8, 6))
sns.violinplot(x=df['Revenue (USD millions)'])
plt.title('Revenue Distribution')
plt.show()
```
Outlier Analysis
```python
# Outliers in Revenue and Employees using Box Plot
plt.figure(figsize=(12, 6))

plt.subplot(1, 2, 1)
sns.boxplot(x=df['Revenue (USD millions)'])
plt.title('Revenue Outliers')

plt.subplot(1, 2, 2)
sns.boxplot(x=df['Employees'])
plt.title('Employee Outliers')

plt.show()
```

## Results

The extracted data includes a detailed list of the largest private companies in the United States by revenue, with columns such as Company Name, Revenue, and Industry.

## Insights and Observations
- Key trends among top private companies by revenue.
- Dominant industries and revenue distribution.

## Future Work
- Scraping data for public companies for comparative analysis.
- Automating the process for regular data updates.

## Acknowledgments
Special thanks to the Wikipedia contributors for maintaining the [source page](https://en.wikipedia.org/wiki/List_of_largest_companies_in_the_United_States_by_revenue).
