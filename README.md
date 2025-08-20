# PowerBI Sales Analysis Dashboard

📌Goal: Analyze sales, profit, margin, and product performance of a company.

🧰Skills: Data Cleaning, PowerQuuery, star schema modeling, DAX measures, data validation, dashboard design.

▶️Process:
- 1️⃣Data Cleaning
  - Removed currency symbols, fixed data types
  - Created Profit column
  - validated Sales = Gross Sales – Discounts, etc.
  
- 2️⃣Data Modeling
  - Built FACT table and DIM tables (Date, Product, Country, Segment, Discount Band)
  - Created Calendar table with Year, Quarter, Month, YearMonthKey
  
- 3️⃣DAX Measures
  - KPIs: Sales Amount, Gross Profit, GP%, Net Margin, etc
  - Time Intelligences: YTD, YoY, Running Total, Moving Average, etc
  - Validation measures to ensure data consistency
  
- 4️⃣Visualizations
  - Page 1: Executive Overview (KPIs, 3M/6M average, YoY, etc)
  - Page 2: Product & Country Breakdown (top prodicts, top countries, map, product profit)
  - Page 3: Profitabiliyt & Drivers (Net Margin, Running Sales, Discount analysis, etc)

💡Key Insights
- Sales peaked in late 2014, strong growth trend
- Channel Partners & Government segments contribute most to GP%
- Top product: Paseo; Top country: USA
- Lower DiscountBand level contributes more to GP%

🗂️Data Source: 
- Source: [Kaggle - Company Financial Dataset](https://www.kaggle.com/datasets/atharvaarya25/financials?resource=download)
- Sumary: Financials CSV (701 rows, includes sales, discounts, products, country, segment, etc.)


