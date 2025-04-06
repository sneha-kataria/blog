---
layout: post
title: "Mastering SQL Through Real-World Business Problems"
author: Sneha
categories: [ product-management ]
tags: [ sql, data-analysis]
image: https://sneha-kataria.github.io/blog/assets/images/hyperlocal_product_recommendation/home_page.png
date: 2024-12-15 10:00:00 -0700
---
Learning SQL is often about syntax, but understanding how to apply it to real-world business challenges is equally 
important. While many tutorials focus on teaching SQL commands, they often miss explaining the thought process behind
solving actual data problems.

In this blog, I share insights from my own SQL practice sessions, breaking down business problems into queries while
illustrating the approach to visualizing and solving them. We'll start with some fundamental queries and gradually
build complexity. To follow along, you can use the Walmart sales dataset for data analysis in PostgreSQL.


## Setting Up Your Database

Before diving into queries, the first step is to create a table in PostgreSQL and upload the dataset. 
Use the following CREATE TABLE command to structure your database with appropriate column names and data types.

```SQL
CREATE TABLE walmart_sales(
    invoice_id VARCHAR(15),             -- string with maximum length of 15 characters.
    branch CHAR(1),    
    city VARCHAR(25),  
    customer_type VARCHAR(15),
    gender VARCHAR(15),
    product_line VARCHAR(55),    
    unit_price FLOAT,                   -- stores numeric values with decimal places.
    quantity INT,                       -- stores integers
    vat FLOAT,
    total FLOAT,
    date date,    
    time time,
    payment_method VARCHAR(15),
    rating FLOAT
);
```

Here is how the table will be created after the above query run: 

![img.png](/blog/assets/images/sql_data_analysis/create_table.png)

Now it is time to feed the data into the table. Once that is done, you can run the below query to select all records 
and it will display all the records. It can also verify that all the data has been transferred to the SQL Database.

```SELECT * from walmart_sales;```

## Business Problems 

### Find the total sales amount for each branch.

```SQL
SELECT branch,                        -- filtering branch column.
SUM(total) as branch_total            -- adding the sales total for each branch and naming it.
FROM walmart_sales
GROUP BY branch;                      -- now grouping the branch column to get the total.
```

**Result:**  

![img.png](/blog/assets/images/sql_data_analysis/Q1.png)

**Insights:**   

The Branch D is performing well with the highest sum of sales whereas Branch A needs to make adjustments 
to increase its sale. 

### Calculate the average customer rating for each city.

```SQL
SELECT city,
AVG(rating) as Avg_rating              -- calculating the average rating for each city.
FROM walmart_sales
GROUP BY city;                         -- now grouping the city column to get distinct value for each city.
```
**Result:**  

![img.png](/blog/assets/images/sql_data_analysis/Q2.png)

**Insights:**  

The average customer rating is between 6 and 7.25 which is good in comparison to each other. 
New plans and strategy should be made to increase the ratings in the city where the average is below 7.


### Count the number of sales transactions for each customer type.

```SQL
SELECT customer_type,
COUNT(*) as total_sales                -- counting each sale made.
FROM walmart_sales
GROUP BY customer_type;                -- grouping by  customer_type column to get total sales made by each type.
```
**Result:**  

![img.png](/blog/assets/images/sql_data_analysis/Q3.png)

**Insights:**  
The number sales made by members and non-members is almost the same in 3 months, hence new discounts and offers could be generated to get more customers to convert into taking the membership. Incentives are not high.

### Find the total quantity of products sold for each product line.

```SQL
SELECT product_line,
COUNT(quantity) as total_quantity    -- Counting total quantity in a new column.
FROM walmart_sales
GROUP BY product_line ;              -- grouping by product_line column to combine the quantity for each category.
```

**Result:**  

![img.png](/blog/assets/images/sql_data_analysis/Q4.png)


**Insights:**  
Health and Beauty department sold slightly less than other departments. So to increase the sales, new ads and offers needs to be launched.

### Calculate the total VAT collected for each payment method.

```SQL
SELECT payment_method,
SUM(vat) as total_vat              -- adding the vat for each payment method.
FROM walmart_sales
GROUP BY payment_method ;          -- now grouping the payment_method column to get total by each method.
```

**Result:**  

![img.png](/blog/assets/images/sql_data_analysis/Q5.png)


**Insights:**  
This data shows people prefer cash or Ewallet transactions more to regulate their spendings. Hence, we could provide smooth credit card transaction process and low rates on high-cost transactions to save customers some bucks.

### Find the total sales amount and average customer rating for each branch.

```SQL
SELECT branch,
SUM(total) as total_sales_amount,                         -- adding the total sales for each branch.
AVG(rating) as Average_rating                             -- calculating the average rating for each branch.
FROM walmart_sales
GROUP BY branch;                                          -- grouping by branch column to get combined data.
```
**Result:**  

![img.png](/blog/assets/images/sql_data_analysis/Q6.png)

**Insights:**  




### Calculate the total sales amount for each city and gender combination.

```SQL
SELECT city, gender,                    -- listing the columns : city is 1 and gender is 2
SUM(total)                              -- calculating the total amount of sales.
FROM walmart_sales
GROUP BY 1, 2;                          -- grouping the data by city and gender columns to get desired results.
```

**Result:**  

![img.png](/blog/assets/images/sql_data_analysis/Q7-1.png)  

![img.png](/blog/assets/images/sql_data_analysis/Q7-2.png)


**Insights:**  




### Find the average quantity of products sold for each product line to female customers.

```SQL
SELECT product_line,
AVG(quantity)                                  -- calculating the average quantity of product sold.
FROM walmart_sales
WHERE gender = 'Female'                        -- filtering only the female customers.
GROUP BY product_line;                         -- grouping by the category of the product.
```

**Result:**  

![img.png](/blog/assets/images/sql_data_analysis/Q8.png)


**Insights:**  





### Count the number of sales transactions for members in each branch.

```SQL
SELECT branch,
COUNT(invoice_id) as No_of_sales                     -- counting the sales made by each branch.
FROM walmart_sales
WHERE customer_type = 'Member'                       -- only by Walmart members.
GROUP BY branch;                                     -- grouping by branch column to get combined data.
```

**Result:**  

![img.png](/blog/assets/images/sql_data_analysis/Q9.png)

**Insights:**  



### Find the total sales amount for each day. (Return day name and their total sales order DESC by amt)

```SQL
SELECT
TO_CHAR (date, 'Day') AS Day_name,                    -- extracting day from the time column.
SUM(total) as total_sales_amount                      -- calculating total sales made.
FROM walmart_sales
GROUP BY Day_name                                      -- grouping by day.
ORDER BY total_sales_amount DESC;                      -- organising the total sales amount in descending order.
```

**Result:**  

![img.png](/blog/assets/images/sql_data_analysis/Q10.png)


**Insights:**  

Stay tuned as we explore more SQL queries, each tied to a specific business problem, to strengthen both your technical skills and analytical thinking.


### Calculate the total sales amount for each hour of the day.

```SQL
SELECT
EXTRACT ( HOUR FROM time) as hours,                 -- extracting hour from the time. Syntax: EXTRACT(part FROM date)
SUM(total) as total_sales_amount                    -- calculating total sales.
FROM walmart_sales
GROUP BY hours                                      -- grouping by day.
order by total_sales_amount;                        -- organising the total sales amount in ascending order(default).
```

**Result:**  

![img.png](/blog/assets/images/sql_data_analysis/Q11.png)

**Insights:**  



### Find the total sales amount for each month. (return month name and their sales)

```SQL
SELECT
TO_CHAR (date, 'month') AS months,                          --converts the date into strings, here in months.
SUM(total) as total_sales_amount                   -- calculating the total of sales amount.
FROM walmart_sales
GROUP BY months                                                                  -- grouping by months.
ORDER BY total_sales_amount DESC;                     -- organising the total sales amount in descending order.
```

**Result:**  


![img.png](/blog/assets/images/sql_data_analysis/Q12.png)

**Insights:**  



### Calculate the total sales amount for each branch where the average customer rating is greater than 7.

```SQL
SELECT branch,
SUM(total) as total_sales_amount,                               -- calculating the total of sales amount.
AVG(rating)                                                     -- calculating the average of ratings.
FROM walmart_sales
GROUP BY branch                                                 -- grouping by branch.
HAVING AVG(rating) > 7;                                         -- where average rating of the branch is greater than 7.
```

**Result:**  

![img.png](/blog/assets/images/sql_data_analysis/Q13.png)


**Insights:**  



### Find the total VAT collected for each product line where the total sales amount is more than 500.

```SQL
SELECT product_line,
SUM(vat) as total_vat                                        -- calculating the total of vat amount.
FROM walmart_sales
GROUP BY product_line                                        -- grouping by product line.
HAVING SUM(vat) > 500 ;                                      -- where sum of vat amount is greater than 500.
```

**Result:**  

![img.png](/blog/assets/images/sql_data_analysis/Q14.png)

**Insights:**  



### Calculate the average sales amount for each gender in each branch.

```SQL
SELECT branch,
gender,
AVG(total) as branch_total                                     -- calculating the average of total sales amount.
FROM walmart_sales
GROUP BY branch, gender                                        -- grouping by branch and gender both.
ORDER BY branch;                                               -- ordering the data by branch.
```

**Result:**  

![img.png](/blog/assets/images/sql_data_analysis/Q15.png)

**Insights:**  



### Count the number of sales transactions for each day of the week.

```SQL
SELECT
TO_CHAR (date, 'Day') AS Day_name,                       --converts the date into strings, here in days.
COUNT(payment_method) as no_of_sales                     --calculating total sales made by different payment method on each day.
FROM walmart_sales
GROUP BY Day_name;                                       --grouping by the day
```

**Result:**  

![img.png](/blog/assets/images/sql_data_analysis/Q16.png)

**Insights:**  



### Find the total sales amount for each city and customer type combination where the number of sales transactions is greater than 50.

```SQL
SELECT city,
customer_type,
SUM(total) as total_sales                              --calculating sum of total sales amount.
FROM walmart_sales
GROUP BY city, customer_type                           --grouping the data by city and customer_type both.
HAVING COUNT(payment_method) > 50                      --providing the results for city where no. of payments are greater than 50.
ORDER BY city;                                         --ordering the data by city.
```

**Result:**  

![img.png](/blog/assets/images/sql_data_analysis/Q17.png)


**Insights:**  


### Calculate the average unit price for each product line and payment method combination.

```SQL
SELECT product_line,                                      --1
payment_method,                                           --2
AVG(unit_price) as avg_unit_price                         --calculating average unit price for each product line.
FROM walmart_sales
GROUP BY 1 , 2                                            --grouping by 1 & 2.
ORDER BY product_line;                                    --ordering by product line.
```

**Result:**  

![img.png](/blog/assets/images/sql_data_analysis/Q18-1.png)
![img.png](/blog/assets/images/sql_data_analysis/Q18-2.png)


**Insights:**  



### Find the total sales amount for each branch and hour of the day combination.

```SQL
SELECT branch,
EXTRACT( HOUR FROM time) AS hour_of_the_day,        --extracting hour from time column.
SUM(total) AS total_sales                           --calculating sum of total sales amount.
FROM walmart_sales
GROUP BY branch, hour_of_the_day                    --grouping by branch and hour.
ORDER BY branch, hour_of_the_day;                   --ordering ths data by branch and hour.
```

**Result:**

![img.png](/blog/assets/images/sql_data_analysis/Q19.png)

**Insights:**  




### Calculate the total sales amount and average customer rating for each product line where the total sales amount is greater than 1000.

```SQL
SELECT product_line,
SUM(total) as total_sales,                                   --calculating sum of total sales amount.
AVG(rating) as avg_rating                                    --calculating the average of rating for each product line.
FROM walmart_sales
GROUP BY product_line                                        --grouping the data by product line.
HAVING SUM(total)  > 1000                                    --where total sales are greater than 1000.
ORDER BY product_line;                                       --ordering by product line.
```
**Result:**  

![img.png](/blog/assets/images/sql_data_analysis/Q20.png)


**Insights:**  



### Calculate the total sales amount for morning (6 AM to 12 PM), afternoon (12 PM to 6 PM), and evening (6 PM to 12 AM) periods using the time condition.

```SQL
SELECT * from walmart_sales;
WITH new_table                                                --creating a new temporary table to convert time into shifts.
AS
(SELECT *,
CASE
WHEN EXTRACT(HOUR FROM time) BETWEEN 6 AND 12 THEN 'Morning'
WHEN EXTRACT(HOUR FROM time) > 12 AND EXTRACT(HOUR FROM time) <= 18 THEN 'Afternoon'
ELSE 'Evening'
END as shifts
FROM walmart_sales)                                         --extracting morning, afternoon and evening shift from the time column.

SELECT shifts,
SUM(total) as total_sales                                   --calculating sum of total sales amount.
FROM new_table
GROUP BY shifts;                                            --grouping the total sales made at each shift.
```

**Result:**  

![img.png](/blog/assets/images/sql_data_analysis/Q21.png)

**Insights:**  


