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

<img width="2416" height="1450" alt="Page1" src="https://github.com/user-attachments/assets/d9c9ae82-31fc-4204-b3d2-23ed8ed63713" />

<img width="2410" height="1432" alt="Page2" src="https://github.com/user-attachments/assets/7219a523-26a0-4e24-97dc-c6b218b1dced" />

<img width="2392" height="1422" alt="Page3" src="https://github.com/user-attachments/assets/3f3aef34-254e-4513-bc3d-6a0f92cc6594" />


💡Key Insights
- Sales peaked in late 2014, strong growth trend
- Channel Partners & Government segments contribute most to GP%
- Top product: Paseo; Top country: USA
- Lower DiscountBand level contributes more to GP%

🗂️Data Source: 
- Source: [Kaggle - Company Financial Dataset](https://www.kaggle.com/datasets/atharvaarya25/financials?resource=download)
- Sumary: Financials CSV (701 rows, includes sales, discounts, products, country, segment, etc.)


