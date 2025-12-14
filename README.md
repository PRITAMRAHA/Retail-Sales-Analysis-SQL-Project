## Retail-Sales-Analysis-SQL-Project

## Project Overview

**Project Title**: Retail Sales Analysis   
**Database**: `SQL - Retail Sales Analysis_utf`

Retail Sales Analysis is an SQL project using MySQL Workbench that focuses on creating and managing a retail database. It involves cleaning sales data, performing exploratory data analysis, and answering business-related questions through SQL queries to extract meaningful insights and demonstrate practical data analysis and database management skills.

## Objectives

1. **Set up a retail sales database**: Design and populate a retail sales database using the provided dataset.
2. **Data Cleaning**: Clean the data by identifying and handling missing or null values.
3. **Exploratory Data Analysis (EDA)**: Conduct exploratory data analysis to understand sales patterns and trends.
4. **Business Analysis**: Apply SQL queries to answer business questions and generate actionable insights.

## Project Structure

### 1. Database Setup

- **Database Creation**: The project starts by creating a database named `PROJECT`.
  
- **Table Creation**: A table named `retail_sales` is created to store the sales data. The table structure includes columns for transaction ID, sale date, sale time, customer ID, gender, age, product category, quantity sold, price per unit, cost of goods sold (COGS), and total sale amount.


First, I will create a database to store the required data. 

```sql
create database PROJECT;
USE PROJECT;

CREATE TABLE retail_sales
(
    transactions_id INT PRIMARY KEY,
    sale_date DATE,	
    sale_time TIME,
    customer_id INT,	
    gender VARCHAR(10),
    age INT,
    category VARCHAR(35),
    quantity INT,
    price_per_unit FLOAT,	
    cogs FLOAT,
    total_sale FLOAT
);
```

### 2. Data Exploration & Cleaning

- **Record Count**: Determine the total number of records in the dataset.
- **Customer Count**: Find out how many unique customers are in the dataset.
- **Category Count**: Identify all unique product categories in the dataset.
- **Null Value Check**: Check for any null values in the dataset and delete records with missing data.

```sql
SELECT COUNT(*) FROM retail_sales;
SELECT COUNT(DISTINCT customer_id) FROM retail_sales;
SELECT DISTINCT category FROM retail_sales;

SELECT * FROM retail_sales
WHERE 
    sale_date IS NULL OR sale_time IS NULL OR customer_id IS NULL OR 
    gender IS NULL OR age IS NULL OR category IS NULL OR 
    quantity IS NULL OR price_per_unit IS NULL OR cogs IS NULL;

DELETE FROM retail_sales
WHERE 
    sale_date IS NULL OR sale_time IS NULL OR customer_id IS NULL OR 
    gender IS NULL OR age IS NULL OR category IS NULL OR 
    quantity IS NULL OR price_per_unit IS NULL OR cogs IS NULL;
```
### 3. Data Analysis & Findings

The following SQL queries were developed to answer specific business questions:

1. **Write a SQL query to retrieve all columns for sales made on '2022-11-05**:
```sql
SELECT *
FROM retail_sales
WHERE sale_date = '2022-11-05';
```

2. **Write a SQL query to retrieve all transactions where the category is 'Clothing' and the quantity sold is more than 4 in the month of Nov-2022**:
```sql
SELECT *
FROM retail_sales
WHERE category = 'Clothing'
  AND sale_date BETWEEN '2022-11-01' AND '2022-11-30'
  AND quantity >= 4;
```

3. **Write a SQL query to calculate the total sales (total_sale) for each category.**:
```sql
SELECT 
    category,
    SUM(total_sale) as net_sale,
    COUNT(*) as total_orders
FROM retail_sales
GROUP BY 1
```

4. **Write a SQL query to find the average age of customers who purchased items from the 'Beauty' category.**:
```sql
SELECT
    ROUND(AVG(age), 2) as avg_age
FROM retail_sales
WHERE category = 'Beauty'
```

5. **Write a SQL query to find all transactions where the total_sale is greater than 1000.**:
```sql
SELECT * FROM retail_sales
WHERE total_sale > 1000
```

6. **Write a SQL query to find the total number of transactions (transaction_id) made by each gender in each category.**:
```sql
SELECT 
    category,
    gender,
    COUNT(*) as total_trans
FROM retail_sales
GROUP 
    BY 
    category,
    gender
ORDER BY total_trans  desc
```

7. **Write a SQL query to calculate the average sale for each month. Find out best selling month in each year**:
```sql
select
    year_,
    month_,
    avg_sales
    from
        (select avg(total_sale) as avg_sales,
        monthname(sale_date) as month_,
        year(sale_date) as year_ ,
        rank() over(partition by year(sale_date) order by avg(total_sale) desc)  rank_
        from retail_sales
            group by year_,month_)as t
where rank_ = 1;
```

8. **Write a SQL query to find the top 5 customers based on the highest total sales **:
```sql
select
    customer_id,
    sum(total_sale) as total_sales
    from retail_sales
        group by customer_id
        order by sum(total_sale)
        desc limit 5;


SELECT 
    customer_id,
    SUM(total_sale) as total_sales
FROM retail_sales
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5;


select
    customer_id,
    sum(total_sale) as total_sales,
    rank () over(order by sum(total_sale) desc) as rank_
    from retail_sales
        group by customer_id
        limit 5;
```

9. **Write a SQL query to find the number of unique customers who purchased items from each category.**:
```sql
select
    category,
    count(distinct customer_id) as NO_OF_CUSTOMER
        from retail_sales

group by category;
```

10. **Write a SQL query to create each shift and number of orders (Example Morning <12, Afternoon Between 12 & 17, Evening >17)**:
```sql

select
shift,
COUNT(*)
from
    (select
        *,
        (case when hour(sale_time) < 12 then "MORNING"
        WHEN hour(sale_time) BETWEEN 12 AND 17 then "AFTERNOON" else "EVENING" END) AS shift
        from retail_sales) T
    GROUP BY shift;



WITH hourly_sale
AS
(
SELECT *,
    CASE
        WHEN EXTRACT(HOUR FROM sale_time) < 12 THEN 'Morning'
        WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
        ELSE 'Evening'
    END as shift
FROM retail_sales
)
SELECT 
    shift,
    COUNT(*) as total_orders    
FROM hourly_sale
GROUP BY shift
```


### **Findings**

* **Customer Demographics:**
  The dataset represents customers from multiple age groups, showing that the retail business caters to a wide and diverse audience. Sales are distributed across different product categories such as Clothing and Beauty, indicating varied customer interests and purchasing behavior.

* **High-Value Transactions:**
  The analysis identified several transactions with total sales exceeding 1000. These high-value purchases suggest the presence of premium products and customers with higher spending capacity, contributing significantly to overall revenue.

* **Sales Trends:**
  Monthly sales analysis reveals noticeable variations in sales performance. Certain months show higher sales volumes, helping to identify peak seasons and periods of increased customer demand.

* **Customer Insights:**
  The data highlights top-spending customers and the most popular product categories. These insights can be used to improve customer retention, optimize product offerings, and develop targeted marketing strategies.


### **Reports**

* **Sales Summary:**
  This report provides a comprehensive overview of overall sales performance, including total revenue, customer demographic distribution, and performance across different product categories. It helps in understanding which segments contribute most to sales.

* **Trend Analysis:**
  The trend analysis report examines sales patterns over different months and shifts, highlighting seasonal variations and periods of high or low sales activity to support better planning and forecasting.

* **Customer Insights:**
  This report focuses on customer behavior by identifying top-spending customers and analyzing the number of unique customers per category, offering valuable insights for customer segmentation and targeted marketing strategies.


### **Conclusion**

The **Retail Sales Analysis SQL Project** demonstrates how structured data can be effectively managed, explored, and analyzed using SQL and MySQL Workbench. By creating and populating a retail database, cleaning the data, performing exploratory analysis, and generating meaningful reports, the project provides actionable insights into customer behavior, sales trends, and high-value transactions. These findings can guide business decisions, optimize product offerings, and enhance marketing strategies. Overall, the project highlights the practical application of SQL in deriving data-driven insights and improving retail business performance.


This project demonstrates the power of SQL in transforming raw sales data into actionable business insights.





























