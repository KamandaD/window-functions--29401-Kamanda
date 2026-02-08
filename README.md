
**Pharmacy Sales Analysis – Advanced SQL (Joins & Window Functions)**

**Project Description**

This project demonstrates advanced SQL concepts using a pharmacy sales database.  
It includes relational joins, ranking functions, navigation functions, and revenue analysis techniques.

The goal is to simulate real-world business intelligence queries using PostgreSQL.
 
---

**Database Schema**

**Tables**

- **customers**
  - customer_id (PK)
  - name
  - region
  - age

- **products**
  - product_id (PK)
  - product_name
  - category
  - price

- **sales**
  - sale_id (PK)
  - customer_id (FK)
  - product_id (FK)
  - amount
  - sale_date

---

**SQL Features Implemented**

**1️ Join Operations**

- INNER JOIN – Valid transactions only
- LEFT JOIN – Customers with no purchases
- RIGHT JOIN – Products not yet sold
- FULL OUTER JOIN – All matched & unmatched records
- SELF JOIN – Customers located in the same region

---

**2️ Ranking Functions**

- ROW_NUMBER()
- RANK()
- DENSE_RANK()
- PERCENT_RANK()
- NTILE()

Used for customer segmentation and revenue ranking by region.

---

**3️ Distribution Functions**

- CUME_DIST()
- NTILE()

Used to categorize customers into spending quartiles.

---

**4️ Navigation Functions**

- LAG() – Monthly revenue comparison
- LEAD() – Forecasting next month sales

Used to calculate:
- Monthly sales
- Previous month sales
- Growth percentage

---

**Example Business Insights**

- Identify high-value customers
- Segment customers into quartiles
- Detect customers without purchases
- Find unsold products
- Compare monthly revenue trends
- Rank customers within each region

---

**Technologies Used**

- PostgreSQL 17
- pgAdmin 4
- SQL (Advanced)

---

**How to Run**

1. Create database
2. Create tables
3. Insert sample data
4. Run analysis queries

---

**Author**

PostgreSQL & SQL Analytics Practice Project
