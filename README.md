**Author:** Ana Ballock | [LinkedIn](linkedin.com/in/anaballock) | [GitHub](github.com/apballock)

## Level 1 – Connectors & Power Query Transformations  
### Exercise 1 – Connect Power BI to MySQL

### Objective

Connect Power BI to a MySQL database to simulate a real-world enterprise data source (instead of flat files).

---

## Database Setup (MySQL)

A local database named **sales_db** was created in MySQL with two tables:

- `products`
- `sales`

After executing the SQL script successfully, the dataset contained:

### Tables Loaded:
- **products → 5 rows**
- **sales → 8 rows**

---

## Power BI Connection Process

The connection was established using:

- Server: `localhost`
- Database: `sales_db`
- Authentication: MySQL root credentials

### 🔧 Issue Encountered

During the connection process, Power BI required an additional component:

> “This connector requires additional components to be installed”

### Solution Applied

The missing dependency was resolved by installing:

- MySQL Connector/NET from official MySQL site

After installation and restarting Power BI, the connection worked successfully.

---

## Result

Both tables were successfully loaded into Power BI:

- products
- sales

The tables became available in the **Data View**, confirming a successful live connection to the MySQL database.

---

## Key Takeaway

This exercise demonstrates a fundamental real-world data workflow:

> Instead of static files, analysts often connect BI tools directly to relational databases.

Understanding how to resolve connector dependencies is essential when working in enterprise environments.

---

## Screenshot

### MySQL Connection in Power BI

![MySQL Connector](screenshots/nivel1_ejerc1_mysql_connector.png)

---

## Exercise 2 – Custom Column in Power Query

### Objective

Create a calculated column in Power Query to classify revenue into business categories, simulating a real ETL transformation step before data modeling.

---

## Transformation Process

A custom column was created in Power Query using the Financial Sample dataset.

### Column Created:
- Revenue Category

### Logic Applied:

Revenue Category logic:
- If Sales > 100000 → "High"
- If Sales > 50000 → "Medium"
- Else → "Low"

---

## Data Quality Issue Encountered

### Issue Identified

A hidden data quality problem was found:

- The column name had a leading whitespace:
  - " Sales" instead of "Sales"

### Impact

This caused:
- Errors in the custom column formula
- Power Query failing to recognize the column correctly

---

## Solution Applied

The issue was resolved by:

- Removing whitespace from the column name
- Reapplying the custom column logic

After correction, the transformation worked successfully.

---

## Visualization Created

A bar chart was built using:

- X-Axis: Revenue Category
- Y-Axis: Sales
- Sorted in descending order

---

## Business Insight

### Revenue Distribution

- High category has the largest share of sales
- Low category is second
- Medium category has the smallest share

### Interpretation

The dataset shows a **polarized revenue structure**:
- Strong concentration in high-value transactions
- Significant low-value volume
- Weak mid-range activity

This may indicate:
- Strong premium customer base
- Opportunity to improve mid-market sales strategy

---

## Key Takeaway

Power Query plays a critical role in data preparation before analysis.

Small data quality issues (like hidden spaces in column names) can break transformations and must be identified early in the ETL process.

---

## Screenshot

![Custom Column Chart](screenshots/nivel1_ejerc2_custom_column.png)

---

## Exercise 3 – Create a Calendar Table with DAX

### Objective

Build a dedicated Calendar Table using DAX to enable proper time intelligence analysis in Power BI.

This is a foundational step in professional data modeling.

---

## Calendar Table Creation

A Calendar Table was created using DAX:

Calendar table logic:
- Date range: 2013–2014
- Includes Year, Month, Quarter, and Day of Week attributes

---

### DAX Formula Used

Calendar =
ADDCOLUMNS(
    CALENDAR(DATE(2013,1,1), DATE(2014,12,31)),
    "Year", YEAR([Date]),
    "MonthNumber", MONTH([Date]),
    "MonthName", FORMAT([Date], "MMMM"),
    "Quarter", "Q" & FORMAT(QUARTER([Date]), "0"),
    "DayOfWeek", FORMAT([Date], "DDDD")
)

---

## Data Model Relationship

A relationship was created between:

- Calendar[Date] → financials[Date]

This enables proper time-based filtering and analysis across the model.

---

## Data Modeling Issue Encountered

### Problem

Initial issues occurred with month sorting:
- Months were displayed in alphabetical order
- Time series visuals were not chronological

### Root Cause

- MonthName is text-based
- Incorrect or missing sort configuration

---

## Solution Applied

The issue was fixed by:

- Ensuring MonthNumber is numeric
- Sorting MonthName by MonthNumber
- Rebuilding Calendar structure with consistent naming

This restored correct chronological ordering.

---

## Validation Test

A line chart was created:

- X-Axis: Calendar[MonthName]
- Y-Axis: Sales

### Result:
- Months displayed correctly from January to December
- Time intelligence structure validated successfully

---

## Business Insight

### Why Calendar Tables Matter

A dedicated Calendar Table is essential because it:

- Enables accurate time intelligence calculations
- Ensures correct filtering across time periods
- Supports advanced DAX functions (YoY, moving averages, etc.)

Without it, time-based analysis becomes unreliable.

---

## Key Takeaway

A strong data model is the foundation of good analytics.

The Calendar Table is not optional in professional Power BI projects — it is a core requirement for time-based analysis.

---

## Screenshot

![Calendar Table Model](screenshots/nivel1_ejerc3_calendar_table.png)

---

## Level 2 – Advanced DAX Functions  
### Exercise 1 – RELATED Function

### Objective

Use the RELATED() DAX function to bring information from one table into another, similar to:

- SQL JOINs
- Excel VLOOKUP/XLOOKUP
- Relational database lookups

This is a fundamental concept in professional Power BI data modeling.

---

## Calculated Columns Created

Using the MySQL tables imported previously (`sales_db sales` and `sales_db products`), three calculated columns were created inside the sales table.

### Product Name

DAXProduct Name =
RELATED('sales_db products'[product_name])

---

### Unit Price

DAXUnit Price =
RELATED('sales_db products'[unit_price])

---

### Total Revenue

DAXTotal Revenue =
'sales_db sales'[units_sold] * 'sales_db sales'[DAXUnit Price]

---

## Issue Encountered

### Problem

The first RELATED() formula initially returned an error saying the column or relationship could not be found.

---

## Root Cause Analysis

Two issues were identified:

### 1. Incorrect Table Name

The imported MySQL tables were not named simply:

- products
- sales

Power BI imported them as:

- sales_db products
- sales_db sales

Because of this, the original DAX formula referenced the wrong table name.

---

### 2. Relationship Validation

The relationship between the tables was verified in Model View using:

- product_id

The relationship existed correctly, confirming the problem was related to naming rather than data modeling.

---

## Solution Applied

The formulas were updated using the correct table names imported by Power BI.

After correction:
- RELATED() successfully retrieved product information
- Revenue calculations worked correctly
- The sales table was enriched with product attributes

---

##  Visualization Created

A bar chart was built using:

- X-Axis: Product Name
- Y-Axis: Total Revenue

The chart was sorted in descending order to identify the highest revenue-generating products.

---

## Business Insight

### Top Revenue Product

Paseo generated the highest total revenue.

### Interpretation

Paseo combines:
- High unit price
- Strong sales volume

This makes it the strongest revenue contributor in the dataset.

---

## RELATED() vs DAX Measure

### RELATED() Calculated Column

- Creates a permanent value stored row-by-row
- Similar to a SQL JOIN enrichment
- Calculated during data refresh

### DAX Measure

- Calculated dynamically
- Depends on filter context and visuals
- Not physically stored in the dataset

---

## Key Takeaway

This exercise demonstrates one of the most important concepts in BI modeling:

Using relationships to enrich fact tables instead of duplicating data manually.

Understanding RELATED() is essential for scalable and normalized Power BI models.

---

## Screenshot

![RELATED Function Chart](screenshots/nivel2_ejerc1_related.png)

---

## Exercise 2 – SUMX Function

### Objective
Use `SUMX()` and `AVERAGEX()` to perform row-by-row calculations before aggregating results.

Unlike `SUM()`, iterator functions such as `SUMX()` evaluate an expression for each row individually, making them essential for advanced financial and revenue calculations.

This exercise simulated a real business scenario:
calculating revenue dynamically from transactional data stored across related tables.

---

## DAX Measures Created

### Total Revenue using SUMX

```DAX
DAXTotal Revenue SUMX =
SUMX(
    'sales_db sales',
    'sales_db sales'[units_sold] * RELATED('sales_db products'[unit_price])
)
```

### Average Revenue per Sale

```DAX
DAXAvg Revenue per Sale =
AVERAGEX(
    'sales_db sales',
    'sales_db sales'[units_sold] * RELATED('sales_db products'[unit_price])
)
```

---

## Matrix Visualization

Built a Matrix visual with:

| Field Area | Value |
|---|---|
| Rows | country |
| Columns | category |
| Values | Total Revenue SUMX |

This structure created a multidimensional revenue breakdown by:
- geography
- product category

A KPI Card was also added to display:
- Average Revenue per Sale

---

## Business Insights

### Highest Revenue Combination

The country + category combination with the highest revenue was:

- **Spain + Bikes**

This result makes business sense because:
- Spain had multiple sales transactions
- Bikes have the highest unit prices in the dataset
- Products like *Paseo* generated particularly strong revenue performance

This demonstrates how combining transaction volume with product pricing reveals the true revenue drivers behind a business.

---

### Average Revenue per Sale

Average Revenue per Sale:

- **3.04k**

This KPI helps estimate:
- average transaction value
- customer purchasing behavior
- product mix quality

Higher average revenue per transaction usually indicates:
- premium products
- higher-volume orders
- stronger monetization efficiency

---

## SUM vs SUMX

| Function | Behavior |
|---|---|
| `SUM()` | Simply adds values from a single column |
| `SUMX()` | Iterates row-by-row, evaluates an expression, then aggregates the results |

### Why SUMX Was Necessary

A regular `SUM()` could not calculate:

```DAX
units_sold * unit_price
```

because:
- `units_sold` exists in the `sales` table
- `unit_price` exists in the `products` table

`SUMX()` solved this by:
1. Iterating through each sales row
2. Pulling the related product price using `RELATED()`
3. Calculating revenue row-by-row
4. Aggregating the final result

This mirrors how revenue calculations are handled in real enterprise BI models.

---

## Issues Encountered & Solutions

### Issue — Confusion Between Measures and Columns

During development, it was necessary to distinguish:
- calculated columns
- DAX measures

At first, there was uncertainty about whether the formulas should be created as columns or measures.

### Solution

The exercise correctly required:
- **Measures** (calculator icon)

because:
- results aggregate dynamically depending on the visual context
- values change automatically based on filters and matrix grouping

This reinforced a key Power BI modeling concept:
- calculated columns are static row-level data
- measures are dynamic context-aware calculations

---

## Technical Learning

This exercise demonstrated several important Power BI concepts:

- Iterator functions (`SUMX`, `AVERAGEX`)
- Relationship-based calculations using `RELATED()`
- Context-aware aggregation
- Matrix visual analysis
- KPI card creation

These are core skills frequently used in:
- financial reporting
- sales analytics
- enterprise BI dashboards

---

## Screenshot

Saved as:
![SUMX](screenshots/nivel2_ejerc2_sumx.png)

---

## Exercise 3 – Organize DAX with a Measures Table

### Objective

Create a dedicated table to centralize all DAX measures in the Power BI model.

This is considered a best practice in professional BI projects because it improves:
- model organization
- scalability
- maintenance
- measure discoverability

---

## Measures Table Creation

An empty table was created using:

DAX_Measures =
ROW("Info", "All measures stored here")

After creation:
- all existing DAX measures were moved into this table
- the Info column was hidden
- the table became a centralized repository for calculations

The table was intentionally named with an underscore prefix:
- `_Measures`

This keeps it pinned near the top of the Data panel for easier navigation.

---

## Measures Organized

The Measures table now contains:

- Profit Margin %
- Avg Sale per Unit
- Total COGS
- DAXTotal Revenue SUMX
- DAXAvg Revenue per Sale
- CROSSFILTER measures

All measures display the calculator icon, improving model readability.

---

## Why This Matters

As Power BI projects grow, measures scattered across multiple tables create:
- maintenance complexity
- navigation confusion
- harder debugging

Centralizing measures creates a cleaner semantic model and improves long-term project scalability.

---

## Key Takeaway

Professional Power BI development is not only about building visuals.

It is also about:
- model governance
- maintainability
- clean architecture

A dedicated Measures table is a standard best practice in enterprise BI environments.

---

## Screenshot

![Measures Table](screenshots/nivel2_ejerc3_measures_table.png)

---

## Level 3 – Filters & CROSSFILTER  
### Exercise 1 – CROSSFILTER Function

### Objective

Use the CROSSFILTER() DAX function to temporarily modify filter direction between related tables.

This allows more flexible analysis when default relationship behavior is not sufficient.

---

## Measures Created

### Products with Sales

DAXProducts with Sales =
CALCULATE(
    COUNTROWS('sales_db products'),
    CROSSFILTER(
        'sales_db sales'[product_id],
        'sales_db products'[product_id],
        BOTH
    )
)

---

### Total Units by Category

Initial approach:

DAXTotal Units by Category =
CALCULATE(
    SUM('sales_db sales'[units_sold]),
    CROSSFILTER(
        'sales_db sales'[product_id],
        'sales_db products'[product_id],
        BOTH
    )
)

---

## Visualization Issue Encountered

### Problem

The measure initially failed when added to the chart.

Power BI returned the message:

> “Ese campo no se puede usar aquí porque requiere un campo que no es de medidas.”

The chart appeared blank or incorrectly configured.

---

## Root Cause Analysis

Two factors caused confusion:

### 1. Horizontal Bar Chart Axis Logic

The visual used was a horizontal bar chart, where:
- X-Axis expects numeric measures
- Y-Axis expects categorical values

Initially, the fields were placed in the opposite configuration.

---

### 2. CROSSFILTER Context Complexity

The original CROSSFILTER measure created ambiguity in the visual context.

A simplified measure using SUMX and CALCULATE was implemented to stabilize aggregation behavior.

---

## Final Working Measure

DAXTotal Units by Category =
SUMX(
    'sales_db products',
    CALCULATE(SUM('sales_db sales'[units_sold]))
)

---

## Visualization Created

A bar chart was built using:

- X-Axis: Total Units by Category
- Y-Axis: category

A Card visual was also added showing:
- Products with Sales = 5

---

## Business Insight

### Top Category by Units Sold

The Bikes category generated the highest number of units sold.

### Interpretation

This suggests:
- strong product-market demand
- higher transaction volume
- Bikes acting as the primary operational revenue driver

Additionally:
- all 5 products registered sales activity
- no inactive inventory was identified in the dataset

---

## CROSSFILTER vs Regular CALCULATE

### Regular CALCULATE
Uses the existing relationship direction defined in the data model.

### CROSSFILTER
Temporarily overrides relationship direction during calculation execution.

This allows:
- bidirectional filtering
- more flexible analytical scenarios
- advanced relationship control

---

## Key Takeaway

This exercise demonstrates an advanced data modeling concept:

> understanding filter propagation is critical for building accurate analytical models.

CROSSFILTER is especially useful in complex enterprise datasets where default relationship behavior is insufficient.

---

## Screenshot

![CROSSFILTER Chart](screenshots/nivel3_ejerc1_crossfilter.png)

---

## Exercise 2 – Report, Page & Visual Level Filters

### Objective
Understand and apply the 3 levels of filtering available in Power BI:

- Visual Level Filters
- Page Level Filters
- Report Level Filters

This exercise demonstrates how filter scope changes the behavior of visuals and report pages in a professional BI environment.

---

## Visual Level Filter

Created a bar chart using:

- Y Axis: Segment
- X Axis: Sales

Applied a **Visual Level Filter** directly to the chart:

- Filter field: `Sales`
- Condition: `Sales > 500000`

### Result
Only segments with total sales above 500k remained visible in that specific visual.

### Real-World Use Case
Visual filters are useful when:
- Highlighting top-performing categories
- Removing noise/outliers from a specific chart
- Creating focused KPIs without affecting the rest of the dashboard

---

## Page Level Filter

Applied a **Page Level Filter**:

- Field: `Country`
- Selected values:
  - USA
  - Canada
  - France

### Result
Every visual on the current page updated automatically to display only data from those 3 countries.

### Real-World Use Case
Page filters are ideal when:
- Building region-specific dashboard pages
- Creating executive views for a business unit
- Comparing selected markets without changing the entire report

---

## Report Level Filter

Applied a **Report Level Filter**:

- Field: `Year`
- Selected value:
  - 2014

### Result
The filter propagated across the entire report, affecting all report pages automatically.

Testing another page confirmed that every visual in the report was restricted to 2014 data.

### Real-World Use Case
Report filters are commonly used when:
- Reports must always display the current fiscal year
- Security or compliance requires global restrictions
- Stakeholders should analyze only a predefined timeframe

---

## Key Differences Between Filter Levels

| Filter Level | Scope | Typical Business Usage |
|---|---|---|
| Visual Filter | Only one visual | Highlight specific KPIs or remove noise |
| Page Filter | Entire report page | Regional or department-focused analysis |
| Report Filter | Entire report | Global constraints and standardized reporting |

---

## Business Insight

This exercise demonstrates how Power BI supports layered analytical storytelling.

By combining different filter scopes, analysts can:
- Maintain consistent global reporting rules
- Build focused regional pages
- Create highly customized visuals without duplicating datasets

This flexibility is essential in enterprise dashboards where executives, managers, and analysts often need different levels of granularity from the same report.

---

## Issues Encountered & Solutions

### Issue — Filter options not visible initially
When applying the Visual Level Filter, the `Sales` field was already automatically present in the Filters panel because it was part of the visual itself.

### Solution
Expanded the existing `Sales` filter card and switched:
- From: `Basic filtering`
- To: `Advanced filtering`

Then configured:
- `Sales > 500000`

This clarified how Power BI auto-generates filter cards based on visual fields.

---

## What Happens When a Report Level Filter Is Applied?

A Report Level Filter affects **every page and visual** in the `.pbix` file.

After applying the `Year = 2014` filter:
- All existing pages updated automatically
- Every visual became restricted to 2014 data
- Cross-page consistency was maintained without manual edits

This behavior is especially important in enterprise reporting environments where centralized control is required.

---

## Screenshot
![FILTERS DEMO](screenshots/nivel3_ejerc2_filters.png)
