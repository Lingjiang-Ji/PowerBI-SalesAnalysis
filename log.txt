log

This document summarizes how the dashboard was built end‑to‑end.

ONE. Data cleaning (Power Query)

dataset: `Financials.csv`. 
0. Power BI auto‑applied *Promoted Headers* and *Changed Type*.

1. Normalize column names
   ```m
   = Table.TransformColumnNames(#"Promoted Headers", each Text.Replace(Text.Trim(_), " ", ""))

2. Currency to decimal: UnitsSold, SalesPrice, GrossSales, ManufacturingPrice, Discounts, Sales, COGS, Profit. Remove $ and cast to decimal.
- For Discounts, replace - with 0.00.
-- Profit had parse errors → Keep Errors to confirm → replace with 0.00 → recreate Profit as decimal.

3. Date parsing (critical): Use Using Locale… → Date, English (Germany) to avoid mm/dd misinterpretation. (wrong parse format at the very beginning)

4. Data consistency in Query:
- GrossSales = UnitsSold * SalesPrice ✅
- Sales = GrossSales - Discounts ✅
- Profit = Sales - COGS ✅
- Define GrossMargin = (GrossSales - COGS) / GrossSales, sanity: Abs(GrossMargin) < 1 ✅
- NetMargin = Profit / Sales (note: original Sales column had a leading space).

TWO. Create star schema

1. FACT‑financials: referenced from raw keep everything for now to match with DIM tables.

2. DIM tables built via reference + dedupe + index:
- DIM-country (CountryID), DIM-product (ProductID), DIM-segment (SegmentID), DIM-discountband (DiscountBandID).

3. create Calendar table:
- Start/end after fixing locale: 2013‑09‑01 to 2014‑12‑02 via DAX:
- Calendar = CALENDAR(DATE(2013,9,1), DATE(2014,12,2))
- Add new columns: Year, Quarter = "Q"&QUARTER([Date]), MonthNumber, Month = FORMAT([Date],"MMM"), YearMonth = FORMAT([Date],"yyyy-MMM"), YearMonthKey = [Year]*100 + [MonthNumber].
- Mark Calendar as Date table; set sorting (Month by MonthNumber, YearMonth by YearMonthKey).

4. build relationships: single‑direction from dimensions to Fact; Calendar[Date] → Fact[Date].

3) Measures
01 base (examples)
Sales Amount := SUM('FACT-financials'[Sales])
Gross Sales  := SUM('FACT-financials'[GrossSales])
Discount Amt := SUM('FACT-financials'[Discounts])
COGS Amount  := SUM('FACT-financials'[COGS])
Units Sold   := SUM('FACT-financials'[UnitsSold])
Gross Profit := [Gross Sales] - [Discount Amt] - [COGS Amount]
GP%          := DIVIDE([Gross Profit],[Gross Sales])
Net Margin (calc) := DIVIDE([Profit Amt], [Sales Amount])
Avg Price    := DIVIDE([Sales Amount], [Units Sold])

02 time (key ones)
Sales Amount (0) := COALESCE([Sales Amount], 0)
Sales PM          := CALCULATE([Sales Amount], DATEADD('Calendar'[Date], -1, MONTH))
Sales MoM         := [Sales Amount] - [Sales PM]
Sales MoM%        := DIVIDE([Sales MoM], [Sales PM])
Sales 3M MA       := AVERAGEX(DATESINPERIOD('Calendar'[Date], MAX('Calendar'[Date]), -3, MONTH), [Sales Amount (0)])
Sales 6M MA       := AVERAGEX(DATESINPERIOD('Calendar'[Date], MAX('Calendar'[Date]), -6, MONTH), [Sales Amount (0)])
Sales PY          := CALCULATE([Sales Amount (0)], SAMEPERIODLASTYEAR('Calendar'[Date]))
Sales YoY         := [Sales Amount (0)] - [Sales PY]
Sales YoY%        := DIVIDE([Sales YoY], [Sales PY])
Sales YTD         := TOTALYTD([Sales Amount], 'Calendar'[Date])
Sales Running     := CALCULATE([Sales Amount], FILTER(ALLSELECTED('Calendar'[Date]), 'Calendar'[Date] <= MAX('Calendar'[Date])))
Active Months     := CALCULATE(DISTINCTCOUNT('Calendar'[YearMonthKey]), FILTER(ALL('Calendar'[Date]), NOT ISBLANK([Sales Amount])))

04 rank
Top Product :=
VAR t = TOPN(1, ADDCOLUMNS(VALUES('DIM-product'[Product]), "SalesVal", [Sales Amount]), [SalesVal], DESC)
RETURN IF(ISEMPTY(t), "-", CONCATENATEX(t, 'DIM-product'[Product] & "-" & FORMAT([SalesVal], "$#,0,,.00M")))

Top Country :=
VAR c = TOPN(1, ADDCOLUMNS(VALUES('DIM-country'[Country]), "SalesVal", [Sales Amount]), [Sales Amount], DESC)
RETURN CONCATENATEX(c, 'DIM-country'[Country], ",")

Highest GP% Product :=
VAR p = TOPN(1, ADDCOLUMNS(VALUES('DIM-product'[Product]), "GPpct", [GP%]), [GPpct], DESC)
RETURN CONCATENATEX(p, 'DIM-product'[Product], ",")

99validation

Row‑match counts for Calendar/Country/Product/Segment/DiscountBand with TREATAS, plus congruity:

SalesCheckDiff  := SUM('FACT-financials'[GrossSales]) - SUM('FACT-financials'[Discounts]) - [Sales Amount]
ProfitCheckDiff := [Sales Amount] - [COGS Amount] - [Profit Amt]
InvalidGrossMarginRows :=
COALESCE(
  CALCULATE(
    COUNTROWS('FACT-financials'),
    FILTER('FACT-financials', ABS(DIVIDE([Gross Sales]-[COGS Amount],[Gross Sales])) > 1)
  ),
0)


Adjusted measures so all checks pass.

4) Report pages & design
Page 1 — Executive Overview

Cards: Sales Amount, Sales YoY%, GP%, Units Sold, Gross Profit, Net Margin (calc), Active Months.

Line: Sales Amount (0) with 3M/6M MA by YearMonth.

Columns: Sales YoY% by YearMonth×Segment; Sales Amount & Gross Profit by Year×Segment.

One shared legend for the two bottom charts; X‑axis set to Categorical to tighten gaps.

Page 2 — Product & Country Breakdown

Cards: Top Product, Top Country, Highest GP% Product.

Bars: Sales Amount by Product.

Pie: Gross Profit by Product.

Stacked column: Sales Amount by Segment×Country.

Map: bubbles by country, colored by segment.

Page 3 — Profitability & Drivers

Slicers: Country, Segment.

Lines: Net Margin (calc) trend; Sales Running by YearMonth.

Columns: GP% by Year×Segment; Gross Profit by Year×Segment.

Combo: Units Sold (column) + Profit Amt (line) by DiscountBand.

5) Lessons learned

Locale matters: parsing Date with the wrong locale collapsed the timeline to 2 active months. Fixing with Using Locale… restored the full range.

Keep a validation page while developing; it saves time chasing model bugs.


