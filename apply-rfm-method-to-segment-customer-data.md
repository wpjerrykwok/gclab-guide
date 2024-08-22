# Apply RFM method to segment customer data

## Activity overview

Data transformation techniques are used to prepare data in a way that makes it easier for users to get the answers to their business questions quickly and efficiently. 

One example of data analysis that often requires transformation is **customer segmentation**.

Segmentation is the process of dividing data into groups based on common characteristics. 

This can be done for a variety of purposes, such as understanding customer behavior, identifying trends, and targeting marketing campaigns.

The **RFM** method is a customer segmentation technique that stands for **recency**, **frequency**, and **monetary** value. 

Recency refers to the time since the customer's last purchase. 

Frequency refers to the number of purchases the customer has made. 

Monetary value refers to the total amount of money the customer has spent.

The RFM method is a data-driven approach to customer segmentation that can be used to identify customers who are most likely to churn, upsell, or cross-sell. 

It can also be used to target marketing campaigns more effectively.

In this lab, you'll use BigQuery to apply the RFM method to segment customer data to help the marketing team decide how to better target their efforts.

## Scenario

TheLook eCommerce has experienced rapid customer growth in recent years. But, despite this growth, customer retention and satisfaction have declined.

As a cloud data analyst for TheLook eCommerce, you’ve been asked to collaborate with Martina, the head of the marketing team, to find a way to better target marketing efforts across the customer base.

To do this, you decide to conduct an RFM analysis by identifying three behaviors for each customer: the recency of their latest purchase, the frequency of purchasing, and the total amount of money spent. 

The RFM method is a customer segmentation technique that will help you rank customers in four groups including **High-Value Customers**, **Loyal Customers**, **At Risk Customers**, and **Persuadable Customers**.

This RFM analysis can help the marketing team identify customer behavior and focus their marketing efforts on these behaviors to keep them engaged.

To complete the RFM analysis, you’ll use transformation techniques to explore a dataset, aggregate data, join data, derive data, and apply a statistical method to the data.

Here’s how you'll do this task:

- analyze the data
- use the orders table to determine the most recent orders and how often customers place orders
- use an inner join to deduce the total amount paid by the top 10 clients in 2022
- create a Common Table Expression (CTE) to consolidate query results
- apply a statistical method to RFM calculations for customer segmentation

## Task 1. Explore the data

In this task, you'll explore the data available in the **thelook_ecommerce** dataset.

In the Cloud console, from the **Navigation menu** (Navigation Menu icon), select **BigQuery**.

Expand the list of datasets by clicking the drop-down arrow next to the project ID.

Scroll down to the `thelook_ecommerce` dataset. Expand the dataset and study the tables listed.

Notice the `orders` and `order_items` tables once the dataset has been expanded.

Select the `orders` table and explore the **Schema**, **Details**, and **Preview** tabs.

On the **Schema** tab, notice the columns (listed under the **Field name** column) in the table. Study the columns along with their **Type** information.

Select the **Details** tab; study the **Table info** and **Storage info** sections.

Select the **Preview** tab; the **Preview** tab displays the first 1000 rows of a table.

On the **Explorer** pane, select the `order_items` table and explore the **Schema**, **Details**, and **Preview** tabs.

## Task 2. Find the 10 most recent orders

Recency refers to how recently a customer made a purchase. 

It is measured in units of time, such as days, weeks, or months. 

A higher recency score indicates that a customer has made a purchase more recently.

In this task, you'll calculate the recency of customer orders to determine how recently each customer placed an order. 

For example, if a customer last made a purchase 400 days ago, their recency score would be 400.

In **BigQuery Studio**, click **+ Compose new query**. A new Untitled tab opens.

Copy the following query into the Query Editor. Take a moment to review the SQL code. Can you anticipate what the query results will look like?

```sql
SELECT
user_id AS customer_id,
DATE_DIFF(CURRENT_TIMESTAMP(), MAX(created_at), DAY) AS recency,
FROM `thelook_ecommerce.orders`
GROUP BY
user_id
ORDER BY recency DESC
LIMIT 10;
```

Click **Run**.

In the **Query results** section on the **Results** tab, a table displays the 10 customers who have placed orders the longest time ago.

## Task 3. Determine the order frequency

Customers who place orders more frequently are considered to be more engaged with the brand and are more likely to make repeat purchases in the future.

In this task, you'll determine the frequency of customer orders, which means for each customer, you'll count the total number of orders they placed. 

For instance, if a customer made 10 separate orders, the frequency for that customer would be 10.

Copy the following query into the Query Editor. Take a moment to review the SQL code. Can you anticipate what the query results will look like?

```sql
SELECT
 user_id AS customer_id,
 COUNT(order_id) as frequency,
FROM `thelook_ecommerce.orders`
WHERE created_at >= '2022-01-01' and created_at < '2023-01-01'
GROUP BY customer_id
ORDER BY frequency DESC
LIMIT 10;
```

Click **Run**.

## Task 4. Determine the total amount spent

Monetary value is the total amount of money a customer has spent with a company over a specified period of time. 

Identifying the customers who spend the most money can help businesses improve customer relationships and target their marketing efforts to their most profitable customers.

In this task, you'll determine the total amount of money spent in 2022 by the top 10 customers. 

To do this you'll use an inner join to connect the `order` and `order_items` tables. You'll also need to use the `SUM()` method to add the sale price.

Copy the following query into the Query Editor. Copy the following query into the Query Editor. Take a moment to review the SQL code. Can you anticipate what the query results will look like?

```sql
SELECT
  o.user_id AS customer_id,
  SUM(oi.sale_price) as monetary
FROM `thelook_ecommerce.orders` o
INNER JOIN `thelook_ecommerce.order_items` oi
ON o.order_id = oi.order_id
WHERE o.created_at >= '2022-01-01' and o.created_at < '2023-01-01'
GROUP BY customer_id
LIMIT 10;
```

Click **Run**.

## Task 5. Create a CTE

A CTE or Common Table Expression is a temporary result set in a SQL query. 

A CTE is created using a `WITH` clause and a named `SELECT` statement. 

CTEs are usually referenced in other `SELECT` statements. 

A CTE makes it easier to write more complex queries without having to create a new table.

In this task, you’ll use a CTE to write a query that combines recency, frequency, and monetary computations you made earlier.

Copy the following query into the Query Editor. Take a moment to review the SQL code. Can you anticipate what the query results will look like?

```sql
WITH
rfm_calc AS (
SELECT
o.user_id AS customer_id,
DATE_DIFF(CURRENT_TIMESTAMP(), MAX(o.created_at), DAY) AS recency,
COUNT(o.order_id) AS frequency,
ROUND(SUM(oi.sale_price)) AS monetary
FROM
`thelook_ecommerce.orders` o
INNER JOIN
`thelook_ecommerce.order_items` oi
ON
o.order_id = oi.order_id
GROUP BY
customer_id )

-- You'll now return values from this CTE
SELECT *
FROM
Rfm_calc;
```

Click **Run**.

## Task 6. Apply a statistical method to RFM calculations

RFM values measure how recently a customer has made a purchase, how frequently they make purchases, and how much they spend each time they purchase something. 

Quantiles divide a set of data into equal parts. 

For example, tertiles divide a set of data into three equal parts and a quartile divides data into four equal parts.

In this task, you'll segment the customers in this example using their RFM values and quantiles. 

You'll use the `NTILE()` function in BigQuery to calculate the quantiles of the RFM values. 

This will give you a better understanding of how your customers are distributed in terms of their RFM values.

To do this, you'll create a new CTE called `rfm_quant`. This CTE will use the `NTILE()` function to calculate the quantiles of the RFM values in the `rfm_calc` CTE that you created in the previous step.

Once you've created the `rfm_quant` CTE, you can use it to segment your customers.

Finally, you'll use the `CASE` statement to assign customer segmentation category names based on the quantiles you produced. 

A `CASE` statement is a SQL statement that allows you to evaluate multiple conditions and return a different value based on which condition is met.

Copy the following query into the Query Editor:

```sql
WITH
rfm_calc AS (
SELECT
o.user_id AS customer_id,
DATE_DIFF(CURRENT_TIMESTAMP(), MAX(o.created_at), DAY) AS recency,
COUNT(o.order_id) AS frequency,
ROUND(SUM(oi.sale_price)) AS monetary
FROM
`thelook_ecommerce.orders` o
INNER JOIN
`thelook_ecommerce.order_items` oi
ON
o.order_id = oi.order_id
GROUP BY
customer_id ),

-- Here you're leveraging the rfm_calc CTE and creating another CTE
rfm_quant AS (
SELECT
customer_id,
NTILE(4) OVER (ORDER BY recency) AS recency_quantile,
NTILE(4) OVER (ORDER BY frequency) AS frequency_quantile,
NTILE(4) OVER (ORDER BY monetary) AS monetary_quantile
FROM
rfm_calc )

--And then you perform a select query that assigns categories based on quantile logic and returns values
SELECT
customer_id,recency_quantile, frequency_quantile, monetary_quantile,
CASE
WHEN monetary_quantile >= 3 AND frequency_quantile >= 3 THEN "High Value Customer"
WHEN frequency_quantile >= 3 THEN "Loyal Customer"
WHEN recency_quantile <= 1 THEN "At Risk Customer"
WHEN recency_quantile >= 3 THEN "Persuadable Customer"
END
AS customer_segment
FROM
rfm_quant;
```

Click **Run**.

## Conclusion

Great work!

You successfully helped Martina better understand their customers by segmenting the data.

First, you analyzed the data.

Second, you used the orders table to determine the most recent orders and how often customers place orders.

Third, you used an inner join to deduce the total amount paid by the top 10 clients in 2022.

Fourth, you created a CTE to consolidate query results.

Finally, you applied a statistical method to RFM calculations for customer segmentation.

You now have practical experience using BigQuery to calculate how recently and how often customers have made purchases. 

You also learned how to calculate how much money they have spent by combining two tables. 

Then, you used those tables and calculations to create a new temporary table. This allowed you to use statistical methods to group customers into segments.

You’re well on your way to understanding how to utilize the RFM method on data sources in BigQuery.
