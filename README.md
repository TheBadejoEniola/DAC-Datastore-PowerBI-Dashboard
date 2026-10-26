# DAC-Datastore-PowerBI-Dashboard

An interactive three-page Power BI dashboard built for D.A.C Datastore, an offline retail store, as the Week 4 project of the Project Builders data analysis training. It turns raw transaction data into answers store management can act on: which products perform best and worst, how sales move over time, how discounts affect revenue, and where customers buy from.

## Project overview

The store sells products across eight categories: Electronics, Footwear, Clothing, Home Appliances, Accessories, Kitchenware, Bags and Personal Care. Management needed one report to monitor sales performance, compare time periods and look up individual orders. This project covers the full workflow: cleaning and shaping the data in Power Query, building a star-schema data model, writing calculations and DAX measures, and designing the dashboard.

## Headline results

| Measure | Value |
|---|---|
| Total orders | 3,510 |
| Total sales | ₹129.5M |
| Total discounts given | ₹7.19M (5.5% of sales) |
| Net sales | ₹122.3M |
| Profit (assumed 10% of net sales) | ₹12.2M |

## Business questions answered

| # | Question | Where to find it |
|---|---|---|
| 1 | Top and bottom 5 products by sales, profit and quantity sold | Product Performance |
| 2 | How do sales trends vary daily, monthly, quarterly and annually? | Sales Overview |
| 3 | What is the relationship between sales and profit? | Sales Overview |
| 4 | How do sales, profit and quantity compare between any two selected periods? | Product Performance |
| 5 | What is the average discount in each discount category? | Sales Overview |
| 6 | What is the total number of orders? | Sales Overview |
| 7 | What are the sales, profit, discount and net sales of each order, filterable by product, date, customer and promotion? | Order Details |
| 8 | How do sales differ by city? | Sales Overview |

## Dashboard pages

### 1. Sales Overview
Headline cards (total orders, total sales, net sales, total profit and average discount), a drill-down line chart that moves from year to quarter, month and day, a bar chart of total sales by city, a sales vs profit scatter plot, and a chart of average discount by promotion.

![Sales Overview](sales-overview.png)

### 2. Product Performance
Six bar charts showing the top 5 and bottom 5 products by sales, profit and units sold, plus monthly trend charts for sales, profit and quantity. A period comparison section uses two slicers (Period A and Period B) to compare sales between any two months, with variance percentage.

![Product Performance](product-performance.png)

### 3. Order Details
A detailed order table with Order ID, date, customer, product, promotion, units sold, price per unit, total sales, discount, net sales and profit, filtered by slicers for product, date, customer ID and promotion.

![Order Details](order-details.png)

## Dataset

The Excel workbook (`Store_Data.xlsx`) contains four tables arranged as a star schema:

| Table | Type | Contents |
|---|---|---|
| Dim Customer | Dimension | 50 customers: ID, name, city, state, pincode, email, phone |
| Dim Product | Dimension | 30 products across 8 categories, with base prices in INR |
| Dim Promotion | Dimension | 5 promotional campaigns and their discount structures |
| Fact table | Fact | 3,510 sales transactions from 2020 to 2024: date, customer, promotion, product and units sold |

## Data preparation (Power Query)

- Generated Order ID: The source dataset did not contain an order ID, so an index column was added in Power Query to identify each transaction row. Each row is counted as one order for this analysis; the generated ID does not establish whether multiple rows originally belonged to the same customer order.
- Renamed the sales sheet to `Fact table` and added an `Order ID` index column.
- Set `Customer ID`, `Product ID` and `Promotion ID` to Text so relationships match.
- Renamed `Price (INR)` to `Price Per Unit`.
- Promoted the first row to headers in Dim Promotion and created a `percentage` column from the promotion type (20, 10, 50 and 70 percent, with Buy 1 Get 1 Free as 50 percent).
- Merged the fact table with Dim Product and Dim Promotion to bring in price and discount percentage.
- Created calculated columns:
  - `Total Sales` = Units Sold × Price Per Unit
  - `Discount Value` = Total Sales × percentage ÷ 100
  - `Net Sales` = Total Sales − Discount Value
  - `Profit` = 10% of Net Sales
- Transactions without a promotion are assigned a 0% discount.

## Data model

Relationships (one-to-many, single direction):

- `Dim Customer[Customer ID]` → `Fact table[Customer ID]`
- `Dim Product[Product ID]` → `Fact table[Product ID]`
- `Dim Promotion[Promotion ID]` → `Fact table[Promotion ID]`
- `Calendar[Date]` → `Fact table[Date]`

A Calendar table provides Year, Quarter, Month and Year-Month fields for time analysis and period comparison.

## DAX measures

- Total Sales, Net Sales, Total Profit, Total Quantity, Total Orders
- Avg Discount % = total discount value ÷ total sales (5.5% overall), so it reflects every order, not just the five promotions
- Period comparison using `CALCULATE` and `TREATAS` with two disconnected period tables: Sales A / Sales B, with variance and variance %

## Assumptions

- **Profit** is calculated as 10% of Net Sales, because the dataset has no cost column. Because of this, sales and profit move in a perfectly straight line on the scatter plot.
- **Buy 1 Get 1 Free** is treated as a 50% effective discount.
- Orders with no promotion are treated as having a 0% discount.
- Each row in the fact table is counted as one order, because the dataset has no order ID.

## Key insights

- **Electronics drives the store.** Electronics made ₹95.4M of the ₹129.5M total sales (73.7%). Personal Care made only ₹0.37M (0.3%).
- **Top products by sales:** Apple iPhone 14 (₹22M, the largest single product at 17% of all sales), Apple MacBook Air (₹21M), Sony Bravia 55" TV (₹21M), Samsung Galaxy S21 (₹16M) and HP Pavilion Laptop (₹15M).
- **Bottom products by sales:** Colgate Toothpaste (₹0.02M), Dove Soap Pack (₹0.09M), Nivea Body Lotion (₹0.09M), L'Oreal Shampoo (₹0.17M) and Tupperware Lunch Box (₹0.28M).
- **Sales trend:** yearly sales fell from ₹33.2M in 2020 to ₹31.2M in 2021 and ₹29.7M in 2022, then recovered to ₹35.3M in 2023 (+18.8%). September 2023 was the best month at ₹4.3M. (2024 holds only one day of data.)
- **Highest-selling city:** Bhopal with ₹16.2M (12.5% of sales), followed by Kanpur (₹15.0M) and Indore (₹14.0M).
- **Discount impact:** only 720 of the 3,510 orders (20.5%) used a promotion, costing ₹7.19M in discounts (5.5% of sales). The Clearance Sale has the highest discount (70%) but produced only ₹1.5M in sales from 59 orders, giving away ₹1.05M of it. The Summer Sale (20% off) produced ₹21.3M from 574 orders.
- **Recommendation:** favour moderate offers like the Summer Sale over 50% to 70% discounts, especially on Electronics.

## Tools used

Power BI Desktop, Power Query, DAX and Microsoft Excel.

## Repository contents

* [Power BI File](DAC_Datastore_Dashboard.pbix)
* [Dataset](Store_Data.xlsx)
* [Page1](sales-overview.png)
* [Page2](product-performance.png)
* [Page3](order-details.png)
* README.md

