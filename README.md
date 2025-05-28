# sql_retail_sales_project



-- DATA CLEANING

    SELECT *
    FROM retail_sales;

    

-- Updating the Date by changing it from stringe which is text to a proper Date./Date formmating

    SELECT `sale_date`
    FROM retail_sales;
    UPDATE retail_sales
    SET `sale_date` = STR_TO_DATE(`sale_date`, '%m/%d/%Y');

-- IDENTIFYING DUPLICATES


      SELECT 
      transactions_id,  sale_date, sale_time, customer_id, gender, age,category, quantiy, price_per_unit, cogs, total_sale, COUNT(*)
      FROM retail_sales
      GROUP BY transactions_id, sale_date, sale_time, customer_id, gender, age,category, quantiy, price_per_unit, cogs, total_sale
      HAVING COUNT(*) > 1;


-- LOOK FOR NULL/BLANK VALUEs AND DELETE

    SELECT *
    FROM retail_sales
    WHERE
    transactions_id IS NULL
    OR
    sale_date IS NULL
    OR
    sale_time IS NULL
    OR
    customer_id IS NULL
    OR
    gender IS NULL
    OR
    age IS NULL
    OR 
    category IS NULL
    OR 
    quantiy IS NULL
    
    OR 
    price_per_unit IS NULL
    OR 
    cogs IS NULL
    
    OR 
    total_sale IS NULL;
    

-- DATA EXPORATION


-- How many sales recorded

    SELECT 
    COUNT(*)
    AS
    total_sale
    FROM retail_sales;

-- How many customers

    SELECT 
    COUNT(customer_id)
    AS
    total_sale
    FROM retail_sales;

-- How many Unique customers

    SELECT 
    COUNT(DISTINCT customer_id)
    AS
    total_sale
    FROM retail_sales;

-- How many categories

    SELECT DISTINCT category 
    FROM retail_sales;


-- SOlving some Basic Business Analyses problem


-- 1 Write a SQL querry to retrieve all columns for sales made on '11/5/2022'

    SELECT *
    FROM retail_sales
    WHERE sale_date = '11/5/2022';

-- 2 write a SQL querry to retrieve all transactions where the category is clothing and quantity sold is more than 0r equals 3

    SELECT * 
    FROM retail_sales
    WHERE category = 'Clothing'
    AND
    quantiy >= 3;

-- 3a write a SQL querry to calculate the total sales.

    SELECT
    SUM(total_sale) AS net_sale
    FROM retail_sales;

-- 3b write  a SQL query to calculate the  total order for each category

    SELECT
    category,
    COUNT(*) AS total_orders
    FROM retail_sales
    GROUP BY 1;

-- 3c write  a SQL query to calculate the  total sales and total order for each category

    SELECT
    category,
    SUM(total_sale) AS net_sale,
    COUNT(*) AS total_order
    FROM retail_sales
    GROUP BY 1;


-- 4 write a SQL querry to find the average age of customers who purchased items from the beauty category,

    SELECT 
    category,
    ROUND(AVG(age), 2) AS avg_age
    FROM retail_sales
    WHERE category = 'Beauty'
    GROUP BY 1;

-- 5 write a SQL querry to find all transactions where the total_sale is greater than 1000

    SELECT *
    FROM retail_sales
    WHERE total_sale > 1000;


-- 6 write SQL querry to find total number of transactions made by each gender in each catgory

    SELECT
    gender, category,
    COUNT(*) AS total_tans
    FROM retail_sales
    GROUP BY gender, category
    ORDER BY 1;


-- 7 write a SQL to calculate the average sale for each month, find out the best selling month in each year

    SELECT
    YEAR(sale_date) as year, -- pick out each year
    MONTH(sale_date) as month, -- pick out each month
    AVG(total_sale) AS avg_total_sale,
    RANK() OVER(PARTITION BY YEAR(sale_date) ORDER BY AVG(total_sale)  DESC) AS total_rank	-- find out rhe best selling month
    FROM retail_sales
    GROUP BY 1,2;
    

-- 8 write a SQL to find the top 5 customers based on the highest total sale


    SELECT 
    customer_id, 
    SUM(total_sale) AS highest_total
    FROM retail_sales
    GROUP BY customer_id
    ORDER BY  2 DESC
    LIMIT 5; -- TOP 5 customer based on highest total sale

-- 9 write a SQL querry to find the number of unique customers who purchased item from unique category


    SELECT 
    category, 
    COUNT(DISTINCT customer_id) AS unique_customer
    FROM retail_sales
    GROUP BY category;

  -- 10 write a SQL querry to create each shift and number of orders , Example(morning <=12, afternoon between 12 & 17 and eveing > 17) and total order recieved
  
  -- Use the case Statement
  
    WITH hourly_sale
    AS
    (
    SELECT *,
    CASE
    WHEN EXTRACT(HOUR FROM sale_time) < 12 THEN 'Morning'
    WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
    ELSE 'Evening'
    END AS shift
    FROM retail_sales
    )
    SELECT
    shift,
    COUNT(*) AS total_orders
    FROM hourly_sale
    GROUP BY shift

