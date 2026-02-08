**Step 1: Problem Definition**
**Business Context**

This project analyzes transactional data from an e-commerce company operating across multiple regions. The analysis supports the Sales and Marketing Department in evaluating product performance, customer behavior, and revenue trends.

**Data Challenge**

Although the company collects detailed transaction data, it lacks analytical reporting to extract business insights. Management requires structured analysis to identify top-performing products, customer segmentation patterns, and monthly sales growth.

**Expected Outcome**

The project will identify high-value customers, rank top products per region, evaluate revenue growth trends, and segment customers into quartiles to support data-driven decision-making.

**Step 2: Success Criteria**

The project aims to achieve the following measurable objectives:

Identify Top 5 products per region using RANK()

Calculate running monthly sales totals using SUM() OVER()

Measure month-over-month sales growth using LAG()

Segment customers into quartiles using NTILE(4)

Compute three-month moving averages using AVG() OVER()

Step 3: Database Schema Design
Tables Created
**1. customers**

customer_id (Primary Key)

customer_name

region

registration_date

**2. products**

product_id (Primary Key)

product_name

category

price

3. sales

**sale_id (Primary Key)**

customer_id (Foreign Key → customers)

product_id (Foreign Key → products)

quantity

sale_date

total_amount

**Relationships**

One customer can make many sales

One product can appear in many sales

Sales table links customers and products

(ER Diagram included in repository)

Step 4: Part A — SQL JOINs Implementation
**1. INNER JOIN**

Retrieve valid transactions with existing customers and products.

SELECT s.sale_id,
       c.customer_name,
       p.product_name,
       s.total_amount
FROM sales s
INNER JOIN customers c ON s.customer_id = c.customer_id
INNER JOIN products p ON s.product_id = p.product_id;
![Image AET](https://github.com/KamandaD/window-functions--29401-Kamanda/blob/5c386530dfb0d72cb01255b46d651f7f74107937/screenshots/inner-join.png)

Business Interpretation:
This query retrieves complete transaction records where customer and product data exist. It ensures only valid and consistent sales records are analyzed.

**2. LEFT JOIN**

Identify customers who have never made a purchase.

SELECT c.customer_id,
       c.customer_name
FROM customers c
LEFT JOIN sales s ON c.customer_id = s.customer_id
WHERE s.sale_id IS NULL;

![Image AET](https://github.com/KamandaD/window-functions--29401-Kamanda/blob/e37f7b04a12e71fc2800291b6b4c7ccf204b9d6f/screenshots/letf-join.png)
Business Interpretation:
This identifies inactive customers who may require targeted marketing campaigns to encourage engagement.

**3. RIGHT JOIN**

Detect products with no sales activity.

SELECT p.product_id,
       p.product_name
FROM sales s
RIGHT JOIN products p ON s.product_id = p.product_id
WHERE s.sale_id IS NULL;


Business Interpretation:
This highlights underperforming products that may require promotional strategies or pricing adjustments.

**4. FULL OUTER JOIN**

Compare customers and products including unmatched records.

SELECT c.customer_name,
       p.product_name,
       s.sale_id
FROM customers c
FULL OUTER JOIN sales s ON c.customer_id = s.customer_id
FULL OUTER JOIN products p ON s.product_id = p.product_id;


Business Interpretation:
Provides a complete overview of relationships between customers and products, including unmatched entries.
![Image AET](https://github.com/KamandaD/window-functions--29401-Kamanda/blob/897972a9d87a3bfb0f92d86bbfdea00db1ff2176/screenshots/FULL%20OUTER%20JOIN.png)
**5. SELF JOIN**

Compare customers within the same region.

SELECT a.customer_name AS customer_a,
       b.customer_name AS customer_b,
       a.region
FROM customers a
JOIN customers b
  ON a.region = b.region
 AND a.customer_id <> b.customer_id;
![Image AET](https://github.com/KamandaD/window-functions--29401-Kamanda/blob/c602e4f1b99f76407ac0d2b9bbddb74fddec0402/screenshots/self-join.png)

Business Interpretation:
This query compares customers from the same region to analyze regional concentration and distribution patterns.

**Step 5: Part B — Window Functions Implementation**
**1. Ranking Functions**
SELECT c.customer_name,
       SUM(s.total_amount) AS total_spent,
       RANK() OVER (ORDER BY SUM(s.total_amount) DESC) AS rank_position,
       DENSE_RANK() OVER (ORDER BY SUM(s.total_amount) DESC) AS dense_rank_position,
       PERCENT_RANK() OVER (ORDER BY SUM(s.total_amount) DESC) AS percent_rank
FROM sales s
JOIN customers c ON s.customer_id = c.customer_id
GROUP BY c.customer_name;


Interpretation:
Ranks customers based on total spending, helping identify top-value clients and prioritize loyalty strategies.

**2. Aggregate Window Functions (Running Total & Moving Average)**
SELECT sale_date,
       SUM(total_amount) AS daily_sales,
       SUM(SUM(total_amount)) OVER (
           ORDER BY sale_date
           ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
       ) AS running_total,
       AVG(SUM(total_amount)) OVER (
           ORDER BY sale_date
           ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
       ) AS three_day_moving_avg
FROM sales
GROUP BY sale_date
ORDER BY sale_date;


Interpretation:
Shows cumulative sales growth and short-term trends using moving averages.

**3. Navigation Functions (LAG)**
SELECT DATE_TRUNC('month', sale_date) AS sale_month,
       SUM(total_amount) AS monthly_sales,
       LAG(SUM(total_amount)) OVER (
           ORDER BY DATE_TRUNC('month', sale_date)
       ) AS previous_month_sales
FROM sales
GROUP BY DATE_TRUNC('month', sale_date);

![Image AET](https://github.com/KamandaD/window-functions--29401-Kamanda/blob/8ae975924b4663acad8110b4ad45b54b6ada0b70/screenshots/Navigation%20Functions.png)
Interpretation:
Compares monthly sales with the previous month to identify growth or decline patterns.

**4. Distribution Functions (Customer Segmentation)**
SELECT customer_id,
       SUM(total_amount) AS total_spent,
       NTILE(4) OVER (ORDER BY SUM(total_amount) DESC) AS spending_quartile,
       CUME_DIST() OVER (ORDER BY SUM(total_amount) DESC) AS cumulative_distribution
FROM sales
GROUP BY customer_id;


Interpretation:
Segments customers into four spending levels, supporting targeted marketing strategies.

**Step 7: Results Analysis**
**Descriptive Analysis**

The data shows variation in revenue across months and significant differences in customer spending behavior.

**Diagnostic Analysis**

Revenue fluctuations appear linked to regional demand differences and product popularity trends.

**Prescriptive Analysis**

The company should focus retention strategies on high-value customers, promote underperforming products, and monitor monthly growth metrics for proactive planning.

**Step 8: References**

PostgreSQL Official Documentation

Oracle SQL Window Functions Guide

Database Development Course Notes

Integrity Statement

“All sources were properly cited. Implementations and analysis represent original work. No AI-generated content was copied without attribution or adaptation.”
