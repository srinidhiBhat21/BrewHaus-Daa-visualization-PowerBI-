# BrewHaus Coffee Sales Dashboard | Power BI

An end-to-end Power BI project for BrewHaus Coffee Co., a fictional cafe chain with 12 stores across 8 Indian cities. The project covers the full workflow: generating the data, cleaning it in Power Query, building the data model, writing DAX measures and designing a two-page interactive dashboard.

⚠️ All data in this project is synthetic. It was generated to behave like a real café business and does not represent any real company.

📌 Table of Contents
Business Problem
Dataset
Tools Used
Data Model
Data Cleaning & Transformation
DAX Measures
Dashboard Pages
Key Insights
Business Recommendations
What I Learned
Repository Structure
How to Use
🎯 Business Problem

BrewHaus management wants one dashboard that answers:

How much revenue and profit is the chain making, and how does it change month to month?
Which stores, cities, regions and store types perform best?
Which products and categories drive sales?
When are the peak hours and days?
How do channels (Dine-in, Takeaway, Delivery App), payment methods and customer types compare?
How satisfied are customers, and where is the experience weakest?

📂 Dataset
Table	Rows	Description
Orders	32,669 order lines (21,202 orders)	Date, hour, store, product, channel, payment method, customer type, quantity, price, cost, discount, rating
Products	24	Product name, category, list price, unit cost
Stores	12	Store name, city, region, store type, opening year, seating capacity
Calendar	365	Date table generated in Power Query (Year, Quarter, Month, Day Name, Weekday/Weekend)

Period: 1 Jan 2025 – 31 Dec 2025 Cities: Bengaluru, Mumbai, Pune, Delhi, Gurugram, Chennai, Hyderabad, Kolkata Store types: High Street, Tech Park, Airport, Mall Kiosk Categories: Hot Coffee, Cold Coffee, Tea, Bakery, Sandwiches, Merchandise

The data generator (scripts/gen_data.py) builds in realistic patterns:

Cold drinks peak in summer and hot drinks in winter
Morning and evening rush hours
Tech parks are busy on weekdays; high streets and malls are busier on weekends
Sales spike during Diwali and Christmas
Airport stores charge a 30% price premium
Delivery orders carry app discounts and get lower ratings

🛠 Tools Used
Power BI Desktop: data modeling, DAX and report design
Power Query (M): data loading, cleaning and calculated columns
Python: synthetic data generation and building the Power BI template

🔄 Data Cleaning & Transformation

Steps in Power Query:

Promoted headers and set the correct data type for every column
Replaced blank ratings with null, so unrated orders don't count as zero
Added calculated columns:
Gross Amount = Quantity × Unit Price
Discount Amount = Gross Amount × Discount % / 100
Net Revenue = Gross Amount − Discount Amount
Total Cost = Quantity × Unit Cost
Profit = Net Revenue − Total Cost
Hour Label (e.g. 08:00) and Daypart (Morning / Afternoon / Evening / Night)
Generated the Calendar table with List.Dates

📐 DAX Measures
Measure	DAX
Total Revenue	SUM(Orders[Net Revenue])
Total Orders	DISTINCTCOUNT(Orders[Order ID])
Items Sold	SUM(Orders[Quantity])
Avg Order Value	DIVIDE([Total Revenue], [Total Orders])
Total Profit	SUM(Orders[Profit])
Profit Margin %	DIVIDE([Total Profit], [Total Revenue])
Discount Given	SUM(Orders[Discount Amount])
Discount % of Gross	DIVIDE([Discount Given], SUM(Orders[Gross Amount]))
Avg Rating	AVERAGE(Orders[Customer Rating])
Rated Orders %	DIVIDE(CALCULATE(DISTINCTCOUNT(Orders[Order ID]), NOT ISBLANK(Orders[Customer Rating])), [Total Orders])
Revenue Prev Month	CALCULATE([Total Revenue], DATEADD('Calendar'[Date], -1, MONTH))
Revenue MoM %	VAR prev = [Revenue Prev Month] RETURN IF(NOT ISBLANK(prev), DIVIDE([Total Revenue] - prev, prev))
Revenue YTD	TOTALYTD([Total Revenue], 'Calendar'[Date])

📊 Dashboard Pages
Page 1: Sales Overview

Sales Overview

KPI cards: Total Revenue, Total Orders, Avg Order Value, Profit Margin, Avg Customer Rating
Combo chart: Monthly Revenue (columns) and Profit Margin % (line)
Donut chart: Revenue by Channel
Bar chart: Revenue by Product Category
Column chart: Orders by Hour of Day
Funnel: Revenue by Region
Page 2: Stores & Products

Stores & Products

KPI cards: Items Sold, Total Profit, Discount Given, Discount % of Gross, Orders with a Rating
Matrix: Store Performance (Revenue, Orders, AOV, Margin, Rating)
Treemap: Revenue by Payment Method
Bar chart: Revenue by Product
Column chart: Avg Order Value by Weekday
Donut chart: Orders by Customer Type

Filters (shared by both pages): City, Store Type, Product Category, Order Channel, Month

💡 Key Insights
KPI	Value
Total Revenue	₹1.04 Cr
Total Orders	21,202
Items Sold	44,950
Avg Order Value	₹489
Total Profit	₹72.8 L
Profit Margin	70.2%
Avg Customer Rating	4.20 / 5
Discount Given	₹2.7 L (2.6% of gross sales)
⏰ Rush hours: 9 AM is the busiest hour, followed by 8 AM, 6 PM and 5 PM
🪔 Seasonality: October is the best month (festive season); February is the slowest
✈️ Airports lead: Kempegowda T2 (Bengaluru) and IGI T3 (Delhi) are the top two stores by revenue, thanks to premium pricing
🛵 Delivery gap: delivery orders get the lowest rating (3.96 vs 4.33 for dine-in)
🏢 Tech-park weekends: tech-park stores earn only 15% of their revenue on weekends
☕ Products: Hot Coffee is the top category (29.8%), followed by Cold Coffee (21.8%); the top products are Cafe Latte, Hazelnut Frappe and Cappuccino
🗺️ Regions: South India brings in 42.9% of revenue; East India only 4.5% (one store)
💳 Payments: UPI accounts for 55.3% of revenue, with cash at just 8.1%
🥡 Channels: Takeaway brings in 39.9% of revenue, Dine-in 39.3% and Delivery App 20.7%
