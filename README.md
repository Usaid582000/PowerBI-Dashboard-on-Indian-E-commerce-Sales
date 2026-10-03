# 📊 Power BI Sales, Product & Customer Analytics Dashboard

An interactive **Power BI Business Intelligence dashboard** built to
analyze sales performance, product and inventory performance, and
customer behavior using three integrated datasets.

## 📌 Project Overview

The project uses:

-   **250,000 sales transactions**
-   **2,000 products**
-   **40,000 customers**

The final dashboard contains three pages:

1.  **Executive Sales Overview**
2.  **Product & Inventory Performance**
3.  **Customer Analytics**

The goal is to transform raw transactional and master data into an
interactive, decision-focused reporting solution using Power Query, DAX,
data modeling, KPIs, and business visualizations.

------------------------------------------------------------------------

## 🎯 Objectives

-   Analyze sales and order performance.
-   Track delivered sales and month-over-month growth.
-   Understand order-status distribution.
-   Compare sales across states, payment modes, and age groups.
-   Identify high-performing product categories, products, and brands.
-   Monitor current stock levels and low-stock products.
-   Analyze the association between discounts and sales.
-   Understand customer demographics and geographic distribution.
-   Compare customer tiers and spending.
-   Identify top customers by delivered sales.
-   Build a professional interactive Power BI dashboard.

------------------------------------------------------------------------

# 📂 Dataset Structure

## 1. `sales.csv`

Transaction-level sales data containing:

  Column                 Description
  ---------------------- -----------------------------------------------------
  `Order_ID`             Unique order identifier
  `Customer_ID`          Customer reference
  `Product_ID`           Product reference
  `Order_Date`           Order date
  `Order_Time`           Order time
  `Delivery_Date`        Delivery date
  `Quantity`             Units ordered
  `Unit_Price`           Price per unit
  `Order_Value`          Order value
  `Shipping_Cost`        Shipping cost
  `Coupon_Code`          Coupon used
  `Coupon_Discount`      Discount amount
  `Total_Amount`         Transaction amount
  `Payment_Mode`         Payment method
  `Order_Status`         Processing, Shipped, Delivered, Cancelled, Returned
  `Rating`               Customer rating
  `Review_Text`          Customer review
  `City`                 Customer city
  `State`                Customer state
  `Customer_Age`         Customer age
  `Customer_Age_Group`   Age category

Derived field:

``` text
Delivery_Days = Delivery_Date - Order_Date
```

## 2. `products.csv`

Product master data containing:

-   `Product_ID`
-   `Product_Name`
-   `Category`
-   `Brand`
-   `Original_Price`
-   `Discount_Percent`
-   `Discount_Amount`
-   `Selling_Price`
-   `Stock_Quantity`
-   `Weight_kg`
-   `Avg_Rating`
-   `Total_Reviews`

Derived fields:

``` text
Discount Rate = Discount_Percent / 100
```

``` text
Stock Quantity < 100       → Low Stock
100–299                    → Medium Stock
300 or more                → Healthy Stock
```

The stock thresholds are analytical definitions and can be changed
according to business requirements.

## 3. `customers.csv`

Customer master data containing:

-   `Customer_ID`
-   `Customer_Name`
-   `Gender`
-   `Age`
-   `Age_Group`
-   `Date_of_Birth`
-   `Email`
-   `Phone`
-   `City`
-   `State`
-   `Pincode`
-   `Registration_Date`
-   `Customer_Tier`
-   `Total_Orders`
-   `Total_Spent`

Email and phone fields are not used in dashboard visuals.

------------------------------------------------------------------------

# 🏗️ Data Model

The project follows a **Star Schema**:

``` text
                     DateTable
                         │
                         │ 1 : *
                         ▼
Products 1 : * ─────── Sales ─────── * : 1 Customers
```

Relationships:

``` text
DateTable[Date]        1 : *  Sales[Order_Date]
Products[Product_ID]   1 : *  Sales[Product_ID]
Customers[Customer_ID] 1 : *  Sales[Customer_ID]
```

Relationships use **single-direction filtering**.

------------------------------------------------------------------------

# 📅 Date Table

``` dax
DateTable =
ADDCOLUMNS(
    CALENDAR(
        MIN(Sales[Order_Date]),
        MAX(Sales[Order_Date])
    ),
    "Year", YEAR([Date]),
    "Month Number", MONTH([Date]),
    "Month", FORMAT([Date], "MMM"),
    "Year Month", FORMAT([Date], "YYYY-MM"),
    "Quarter", "Q" & FORMAT([Date], "Q"),
    "Day", DAY([Date]),
    "Day Name", FORMAT([Date], "DDD")
)
```

The Date Table is marked as the official Power BI Date Table.

------------------------------------------------------------------------

# 📊 Dashboard Pages

## Page 1 --- Executive Sales Overview

### KPIs

-   Delivered Sales
-   Total Orders
-   Total Units Sold
-   Average Order Value
-   Month-over-Month Sales Growth
-   Delivery Rate

### Visuals

-   Monthly Delivered Sales vs Previous Month
-   Order Status Distribution
-   Delivered Sales by State
-   Orders by Payment Mode
-   Delivered Sales by Customer Age Group

### Filters

-   Year
-   State
-   Order Status
-   Payment Mode
-   Customer Age Group

------------------------------------------------------------------------

## Page 2 --- Product & Inventory Performance

### KPIs

-   Total Products
-   Total Inventory
-   Low Stock Products
-   Average Selling Price
-   Average Discount

### Visuals

-   Delivered Sales by Category
-   Delivered Sales by Product
-   Delivered Sales by Brand
-   Total Units Sold by Category
-   Discount vs Delivered Sales
-   Product Rating vs Sales Performance
-   Product Count by Stock Status
-   Original Price vs Selling Price
-   Product performance table

### Product table

Includes:

-   Product Name
-   Category
-   Brand
-   Stock Quantity
-   Selling Price
-   Discount Rate
-   Average Rating
-   Total Reviews
-   Delivered Sales
-   Total Units Sold

------------------------------------------------------------------------

## Page 3 --- Customer Analytics

### KPIs

-   Total Customers
-   Active Customers
-   Customer Sales
-   Average Customer Spend
-   Average Orders per Customer

### Visuals

-   Customer Sales by Age Group
-   Delivered Sales by Customer Tier
-   Active Customers by State
-   Customer Distribution by Gender
-   Customer Orders vs Delivered Sales
-   Active Customers by Customer Tier
-   Top 10 Customers by Delivered Sales

### Filters

-   Gender
-   Age Group
-   Customer Tier
-   State
-   City

------------------------------------------------------------------------

# 🧮 Key DAX Measures

### Total Orders

``` dax
Total Orders =
DISTINCTCOUNT(Sales[Order_ID])
```

### Total Units Sold

``` dax
Total Units Sold =
SUM(Sales[Quantity])
```

### Delivered Sales

``` dax
Delivered Sales =
CALCULATE(
    [Sales Value],
    Sales[Order_Status] = "Delivered"
)
```

### Average Order Value

``` dax
Average Order Value =
DIVIDE(
    [Sales Value],
    [Total Orders]
)
```

### Delivery Rate

``` dax
Delivery Rate =
DIVIDE(
    [Delivered Orders],
    [Total Orders]
)
```

### Month-over-Month Sales Growth

``` dax
MoM Sales Growth =
DIVIDE(
    [Delivered Sales] - [Previous Month Delivered Sales],
    [Previous Month Delivered Sales]
)
```

### Total Customers

``` dax
Total Customers =
DISTINCTCOUNT(Customers[Customer_ID])
```

### Active Customers

``` dax
Active Customers =
CALCULATE(
    DISTINCTCOUNT(Sales[Customer_ID]),
    Sales[Order_Status] = "Delivered"
)
```

### Average Customer Spend

``` dax
Average Customer Spend =
DIVIDE(
    [Customer Sales],
    [Active Customers]
)
```

------------------------------------------------------------------------

# 🔄 Data Preparation

Data preparation was performed using **Power Query**:

-   Imported the three CSV datasets.
-   Validated data types.
-   Created `Delivery_Days`.
-   Created `Discount Rate`.
-   Created `Stock Status`.
-   Preserved meaningful blank values.
-   Created a dedicated Date Table.
-   Established relationships between fact and dimension tables.
-   Built reusable DAX measures for dashboard KPIs.

------------------------------------------------------------------------

# 🎨 Dashboard Features

-   Interactive slicers
-   KPI cards
-   Line charts
-   Bar and column charts
-   Donut charts
-   Scatter plots
-   Detailed tables
-   Conditional formatting
-   Reset Filters buttons
-   Back / Next page navigation
-   Consistent visual design across all three pages

Navigation:

``` text
Executive Sales Overview
          ↓
Product & Inventory Performance
          ↓
Customer Analytics
```

------------------------------------------------------------------------

# 🔍 Analytical Considerations

### Delivered Sales vs Total Amount

The main sales KPI uses **Delivered Sales** rather than treating every
transaction as completed sales. This avoids presenting cancelled,
returned, processing, and shipped transactions as completed sales.

### Profit

Profit and profit margin are **not calculated** because the dataset does
not provide COGS or a reliable profit field.

### Inventory Turnover

Inventory turnover is **not calculated** because historical inventory
snapshots and COGS are unavailable.

### Discount Analysis

The **Discount vs Delivered Sales** chart shows an association between
discount rate and sales. It should not be interpreted as proof that
discounts cause higher or lower sales.

### Customer Tier

Customer Tier is presented as a descriptive field. Any business
interpretation of Platinum, Gold, or Silver should be based on the
documented rules used to create those tiers.

------------------------------------------------------------------------

# 🛠️ Technologies Used

-   **Microsoft Power BI**
-   **Power Query**
-   **DAX**
-   **CSV**
-   **Data Modeling**
-   **Star Schema**
-   **Data Visualization**
-   **Business Intelligence**

------------------------------------------------------------------------

# 🚀 How to Use

1.  Download or clone the repository.
2.  Open the `.pbix` file using **Microsoft Power BI Desktop**.
3.  If the CSV paths are unavailable, update the data source locations
    in Power Query.
4.  Select **Refresh**.
5.  Use the slicers to explore the dashboard.
6.  Navigate between the three pages using the Back and Next buttons.

------------------------------------------------------------------------

# 📈 Project Outcome

This project demonstrates an end-to-end **Business Intelligence
workflow**:

``` text
Raw CSV Data
     ↓
Power Query
     ↓
Data Cleaning
     ↓
Star Schema
     ↓
DAX Measures
     ↓
KPIs & Visualizations
     ↓
Interactive Power BI Dashboard
```

The project demonstrates practical skills in:

**Data Cleaning → Data Modeling → DAX → KPI Development → Data
Visualization → Business Analysis → Dashboard Design**

------------------------------------------------------------------------

## 👨‍💻 Author

**Usaid.D**

Data Analytics \| Data Science \| Power BI

------------------------------------------------------------------------

## ⭐ Project

A practical Power BI project focused on transforming raw business data
into interactive and actionable analytics.
