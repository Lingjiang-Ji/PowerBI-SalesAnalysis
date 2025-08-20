# PowerBI Sales Analysis Dashboard

📌Goal: Analyze sales, profit, margin, and product performance of a company.

🧰Skills: Data Cleaning, PowerQuuery, star schema modeling, DAX measures, data validation, dashboard design.

▶️Process:

1️⃣Data Cleaning
- ** ** Removed currency symbols, fixed data types
  
  Created Profit column
  
  validated Sales = Gross Sales – Discounts, etc.
  
2️⃣Data Modeling
  Built FACT table and DIM tables (Date, Product, Country, Segment, Discount Band)
  
  Created Calendar table with Year, Quarter, Month, YearMonthKey
  
3️⃣DAX Measures
  KPIs: Sales Amount, Gross Profit, GP%, Net Margin, etc
  
  Time Intelligences: YTD, YoY, Running Total, Moving Average, etc
  
  Validation measures to ensure data consistency
  
4️⃣Visualizations
  Page 1: Executive Overview (KPIs, 3M/6M average, YoY, etc)
  
  Page 2: Product & Country Breakdown (top prodicts, top countries, map, product profit)

  Page 3: Profitabiliyt & Drivers (Net Margin, Running Sales, Discount analysis, etc)

🗂️Data Source: 
- **Source**: [Kaggle - Netflix Movies and TV Shows Dataset](https://www.kaggle.com/datasets/shivamb/netflix-shows)
- **Size**: ~8,800 records
- **File Format**: CSV
- **Key Columns**:
  - `type` – Movie or TV Show
  - `title` – Title of the content
  - `country` –
Input: Financials CSV (701 rows, includes sales, discounts, products, country, segment, etc.)

Note: This is the standard sample dataset bundled with Power BI (safe to share).
