# Olist E-Commerce Analytics - Power BI Dashboard

An interactive 3-page Power BI dashboard built on top of the Olist SQL Analysis project.
Connects directly to a live MySQL database to visualize 100k+ orders across revenue, customer behaviour, and delivery performance.

---

## Project Context

This dashboard is the visualization layer of the Olist E-Commerce project.
The SQL analysis uncovered 25 business insights - this dashboard makes those insights consumable for stakeholders who don't read SQL.

Pipeline:
```
Kaggle CSV Files -> MySQL 8.0 (via LOAD DATA INFILE) -> Power BI Desktop (Live Connection) -> Dashboard
```

---

## Files

```
olist-ecommerce-analysis/
│
├── olist_db_setup.sql              # Schema design and data import
├── olist_ecommerce_analysis.sql    # 25 SQL business analysis queries
├── olist_dashboard.pbix            # Power BI dashboard file
└── README.md
```

---

## Dashboard Pages

### Page 1 - Business Overview
High level performance summary for executive stakeholders.

| Visual | Insight |
|---|---|
| KPI - Total Revenue | 16.01M BRL total across 2016-2018 |
| KPI - Total Orders | 99,441 orders processed |
| KPI - Avg Order Value | 160.99 BRL per order |
| KPI - Late Delivery Rate | 8.11% of delivered orders arrived late |
| Line Chart - Monthly Revenue Trend | Actual revenue vs 3-month rolling average |
| Bar Chart - Top 10 Categories by Revenue | Health and Beauty leads at 1.26M BRL |

---

### Page 2 - Customer Insights
Customer behaviour, retention, and segmentation analysis.

| Visual | Insight |
|---|---|
| KPI - Total Customers | 96k unique customers |
| KPI - Repeat Customer Rate | Only 3.12% of customers reordered |
| KPI - Avg Customer Lifetime Value | 166.59 BRL per customer |
| Bar Chart - Revenue by State | SP drives 3.73M BRL, nearly 3x more than RJ |
| Donut Chart - Orders by Payment Type | Credit card dominates at 77k transactions |
| Bar Chart - Customer Segments | 92k Low / 3k Mid / 1k High value customers |

---

### Page 3 - Delivery and Seller Performance
Operational performance across delivery times, cancellations, and product ratings.

| Visual | Insight |
|---|---|
| KPI - Avg Delivery Time | 12.50 days average from order to delivery |
| KPI - Late Deliveries | 7,826 orders delivered after estimated date |
| Line Chart - Monthly Cancellation Rate | Cancellation rate dropped sharply after 2016 |
| Bar Chart - Slowest Delivery Categories | cine_photo and drinks take the longest |
| Bar Chart - Lowest Rated Categories | security_and_services has the lowest avg score |

---

## Technical Implementation

### Data Connection
- Connected Power BI Desktop directly to MySQL localhost with no CSV exports
- All 8 Olist tables loaded via the live MySQL connection
- Relationships auto-detected and manually verified in Model view

### DAX Measures Built

```
Total Revenue         = ROUND(SUM(order_payments[payment_value]), 2)
Total Orders          = DISTINCTCOUNT(orders[order_id])
Avg Order Value       = ROUND(DIVIDE([Total Revenue], [Total Orders]), 2)
Late Delivery Rate    = % of delivered orders past estimated date
Repeat Customer %     = % of customers with more than 1 order
Avg CLV               = Average total spend per unique customer
Monthly Revenue       = Monthly SUM of payment values
3 Month Rolling Avg   = Rolling average using ROWS BETWEEN window
Cancellation Rate %   = Monthly canceled / total orders x 100
Avg Delivery Days     = AVERAGEX using DATEDIFF on delivered orders
Category Revenue      = SUM via order_items bridge table
Payment Type Count    = COUNTROWS directly from order_payments
```

### Calculated Table
```
Customer Tiers = ADDCOLUMNS + SUMMARIZE
```
Segments 96k customers into High (above 1000 BRL), Mid (500-1000 BRL), and Low (below 500 BRL) tiers based on total lifetime spend.

### Key Technical Challenges Solved

**Cross-filter direction conflict**
Top N filters and category-level measures were returning identical values across all categories due to broken filter context across a 4-table chain (categories -> products -> order_items -> orders). Fixed by setting cross-filter direction to Both on the order_items relationships in Model view.

**Collapsible Slicer Panel**
Built using Power BI Bookmarks - two bookmarks (Panel Open / Panel Closed) toggled by blank buttons with Action type set to Bookmark. Gives a clean canvas without permanently hiding filter controls.

**Date slicer showing day/month/year**
Fixed by changing the date field from Date Hierarchy to Year only, showing 2016, 2017, 2018 as clean tile options.

---

## How to Open

1. Clone or download this repository
2. Open olist_dashboard.pbix in Power BI Desktop
3. Go to Transform Data -> Data Source Settings
4. Update the MySQL connection to point to your local MySQL instance with the olist database
5. Click Refresh and all visuals will populate from your local database

Note: If you don't have MySQL set up locally, run olist_db_setup.sql first to create and populate the database.

---

## Key Business Insights

- 97% of customers never placed a second order - retention is the core problem
- SP state generates nearly 3x more revenue than any other state at 3.73M BRL
- Credit card accounts for 77k of 103k total transactions
- Health and Beauty leads all categories at 1.26M BRL in revenue
- Only 8.11% of orders arrived late, so delivery performance is mostly reliable
- Average CLV of 166 BRL per customer points to a one-time purchase pattern across the base

---

## Related Projects

Olist SQL Analysis - the SQL layer this dashboard is built on top of

---

## Tools Used

| Tool | Purpose |
|---|---|
| Power BI Desktop | Dashboard development |
| MySQL 8.0 | Data source via live connection |
| DAX | Measures and calculated tables |
| Power Query | Column type fixes on import |

---

## Author

Tharun Sajeev
Aspiring Data Analyst
LinkedIn: https://linkedin.com/in/tharun-sajeev-8a9b12285
GitHub: https://github.com/tharunsajeev
