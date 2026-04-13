Supply Chain Performance Dashboard (2015–2018)

Project Overview

This project analyzes 4 years of global supply chain data to track revenue performance, identify profitability trends, and evaluate operational efficiency. Using Power BI, I built a three-page interactive dashboard showing supply chain performance from 2015 to 2018 — the growth, the declines, and where the business is losing money.

The Core Question: How has supply chain performance evolved year-over-year, which products and customer segments drive the most value, and where are the operational inefficiencies?

Dataset

- **Source:** DataCo Smart Supply Chain Dataset from [Kaggle](https://www.kaggle.com/datasets/shashwatwork/dataco-smart-supply-chain-for-big-data-analysis)
Size: 180,519 rows covering orders, customers, products, shipping and financials
Time Period: January 2015 – December 2018
Geography: United States, Puerto Rico, Africa, Europe, LATAM, Pacific Asia, USCA

Key data points:Order dates, sales revenue, profit, customer segments, product categories, shipping modes, delivery status, and payment types.

-----

Tools Used

Power BI Desktop — Data modeling, DAX, visualization
Power Query — Data cleaning and transformation
DAX — Time intelligence and calculations


Data Cleaning & Transformation

Power Query cleaning steps:

Fixed critical data type issue: Converted Order Date and Ship Date from DateTime to Date — this was breaking all time intelligence relationships
- **Removed unnecessary columns:** Product images, passwords, emails, zipcodes, coordinates
- **Replaced country codes:** Changed “EE. UU.” (Spanish for USA) to “United States”
- **Merged name columns:** Combined first and last name into Customer Full Name
- **Created Days To Ship:** Calculated duration between order date and ship date
- **Handled nulls:** Replaced with 0 for numbers, “Unknown” for text, removed rows with null dates

-----

## Data Modeling

Built a **Star Schema** — one central fact table surrounded by dimension tables for clean relationships and optimal performance.

**Fact_Orders** (180,519 rows) — All transactions with sales, profit, quantity, discount, and foreign keys linking to dimensions

**Dim_Customer** (20,652 unique customers) — Customer names, segments (Consumer/Corporate/Home Office), and locations

**Dim_Product** (118 unique products) — Product names, categories, departments, and prices

**Dim_Date** (1,461 rows) — Complete calendar from 2015-01-01 to 2018-12-31 with no gaps, built in DAX and marked as the official Date Table

**Relationships:** All dimension tables connect to Fact_Orders using one-to-many relationships with single-direction filtering. The Date table connects via Order Date (active relationship).

-----

## DAX Measures

All measures stored in a dedicated `_Measures` table.

**Base Measures:**

```dax
Total Sales = SUM(Fact_Orders[Sales])
Total Profit = SUM(Fact_Orders[Order Profit Per Order])
Total Orders = DISTINCTCOUNT(Fact_Orders[Order Id])
Profit Margin % = DIVIDE([Total Profit], [Total Sales], 0)
Average Order Value = DIVIDE([Total Sales], [Total Orders], 0)
```

**Time Intelligence — Year-over-Year:**

```dax
Sales Previous Year =
IF(
HASONEVALUE(Dim_Date[Month Name]),
CALCULATE([Total Sales], SAMEPERIODLASTYEAR(Dim_Date[Date])),
BLANK()
)

YoY Sales % Change =
IF(
HASONEVALUE(Dim_Date[Month Name]),
DIVIDE([Total Sales] - [Sales Previous Year], [Sales Previous Year], 0),
BLANK()
)
```

**Time Intelligence — Month-over-Month:**

```dax
Sales Previous Month =
IF(
HASONEVALUE(Dim_Date[Month Name]),
CALCULATE([Total Sales], DATEADD(Dim_Date[Date], -1, MONTH)),
BLANK()
)

MoM Sales % Change =
IF(
HASONEVALUE(Dim_Date[Month Name]),
DIVIDE([Total Sales] - [Sales Previous Month], [Sales Previous Month], 0),
BLANK()
)
```

**Running Totals:**

```dax
Sales YTD = TOTALYTD([Total Sales], Dim_Date[Date])
Sales QTD = TOTALQTD([Total Sales], Dim_Date[Date])
Profit YTD = TOTALYTD([Total Profit], Dim_Date[Date])
```

*Note: Similar measures created for Profit MoM, Profit YoY, and Orders YTD following the same pattern.*

-----

## Dashboard Pages

### Page 1: Executive Summary

High-level business performance overview with KPI cards showing Total Sales ($37M), Total Customers (21K), Total Products (118), and Average Order Value ($559). Includes monthly sales trend line, sales by customer segment donut chart, sales by shipping mode bar chart, and navigation to other pages.

### Page 2: Performance Trends

Deep dive into time intelligence with YTD, QTD, YoY and MoM KPIs. Features a matrix table showing Year → Month with conditional formatting (green for growth, red for decline), current vs previous year line charts by month and quarter, and running total cards for profit and orders.

### Page 3: Customer and Product Insights

Operational analysis featuring Top 10 products by sales, sales by product category column chart, sales distribution by country, and a segment performance table comparing Consumer, Corporate, and Home Office customers across sales, profit, orders and margins.


Key Insights

Consumer segment dominates volume: with 51.91% of sales and 34,118 orders, but profit margins across all segments are below 1% — a critical sustainability concern
Standard Class shipping accounts for 59% of revenue ($22M of $37M), suggesting customers prioritize cost over speed
Puerto Rico represents 38% of sales distribution, nearly matching the United States at 62% — a strategically important market
Fishing category leads product revenue at $6.9M, followed by Cleats ($4.4M) and Camping & Hiking ($4.1M)
Late delivery rate is a persistent problem across the dataset, directly impacting customer satisfaction
2018 data is incomplete, showing significantly lower transaction volume — YoY comparisons should be interpreted with caution



Challenges & Solutions

DateTime vs Date Mismatch — Order Date stored as DateTime couldn’t match the Calendar table’s plain Date values. Relationship appeared active but was silently failing. Converted to Date Only in Power Query and sales jumped from $299 to $300,000.

Accidental Row Loss — Removed duplicates from Fact_Orders (transaction table), dropping from 180,519 rows to 65,652. Lesson: only remove duplicates from dimension tables, never fact tables.

Many-to-Many Relationships  — Shipping Mode appeared with different statuses, breaking dimension table logic. Solution: deleted Dim_ShipMode entirely and kept columns in Fact_Orders.

Time Intelligence Totals — Without HASONEVALUE() wrapping measures, total rows showed nonsensical aggregated values. Fixed by forcing BLANK() returns on total rows.

Month Sorting — Power BI sorted months alphabetically (April before January). Fixed by setting Month Name column to sort by Month Number.



Skills Demonstrated

Data Engineering: Power Query ETL, data cleaning, type correction, star schema design, relationship management

DAX: Time intelligence (YoY, MoM, YTD, QTD), CALCULATE, DATEADD, SAMEPERIODLASTYEAR, TOTALYTD, HASONEVALUE, DIVIDE, DISTINCTCOUNT, calendar table creation

Visualization: Multi-page dashboards, KPI cards with sparklines, conditional formatting, matrix tables, navigation design, Top N filtering




About This Project

This dashboard was built as part of my ongoing Power BI learning journey, documented in public on LinkedIn and Medium. Every mistake, error message, and breakthrough is part of the process. The goal was not just to build something that looks good but to understand WHY every decision was made, from the data model to the DAX formula to the visual choice.

Connect With Me

If you have feedback, suggestions, or you’re on a similar learning journey, I’d love to connect.

LinkedIn: http://linkedin.com/in/ife-okoli

Medium: https://medium.com/@ifechukwuokoli97
