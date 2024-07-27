# Walmart_Sales_Analysis
I am excited to share my first project, "Sales and Revenue Analysis of Walmart Sales." Utilizing SQL for data processing and Excel for visualization, I conducted an in-depth analysis of Walmart's sales data. This project aims to provide valuable insights into sales performance, average pricing, and revenue trends across various dimensions, including geography, product categories, payment methods, and customer demographics. The findings from this analysis can inform strategic decision-making and optimize business operations.

Key Insights:
-Identified seasonal trends and patterns in sales performance.
-Highlighted top-performing countries, cities, and product categories.
-Analyzed customer preferences for different payment methods and demographic trends.

MySQL Code : 
create database walmart_sales_analysis;
use walmart_sales_analysis;


select * from sales_analysis;


------ **Feature Engineering**-----
/*Time_of_day*/

SELECT time,
	(CASE 
		WHEN time BETWEEN "00:00:00" AND "12:00:00" THEN 'Morning'
		WHEN time BETWEEN "12:01:00" AND "16:00:00" THEN 'Afternoon'
		ELSE 'Evening'
	END
	) AS time_of_date
from sales_analysis;

Alter table sales_analysis ADD COLUMN time_of_day VARCHAR(20);
UPDATE sales_analysis
SET time_of_day = (
	CASE 
		WHEN time BETWEEN "00:00:00" AND "11:59:59" THEN "Morning"
		WHEN time BETWEEN "12:00:00" AND "16:59:59" THEN "Afternoon"
		ELSE "Evening"
	END);


--------------/*Day_Name*/----------
select Date, dayname(Date) as day_name from sales_analysis;
Alter table sales_analysis ADD COLUMN day_name VARCHAR(10);
update sales_analysis 
SET day_name = dayname(date);

--------------/*Month_Name*/----------
select date, monthname(date) as month_name from sales_analysis;
Alter table sales_analysis ADD COLUMN month_name VARCHAR(10);
UPDATE sales_analysis
SET month_name = monthname(date);

----------------------------------------------------------------------------------------
----------------------------------------------------------------------------------------
----------------------------/*Generic Questions*/---------------------------------------
-----------/*How many unique cities does the data have?*/-------------------------------
select distinct city from sales_analysis;


-----------/*How many unique branches does the data have?*/------------------------------
select distinct branch from sales_analysis;

-----------/*In which city is each branch?*/---------------------------------------------
select distinct city , branch from sales_analysis;

---------------/*About Products*/-----------
-------/*How many unique product lines does the data have?*/-------------------------------
SELECT count(DISTINCT Product_line) from sales_analysis;

-------/*What is the most common payment method?*/---------------------------------------
select payment, count(payment) as cnt
from sales_analysis
group by payment
order by cnt desc;

-----/*What is the most selling product line?*/---------------------------------------------
select product_line, count(product_line) as cnt
from sales_analysis
group by product_line
order by cnt desc;

-------/*What is the total revenue by month?*/--------------------------------------------
select month_name as month,
SUM(TOTAL) as total_revenue from sales_analysis
group by month_name
order by total_revenue desc;

------/*What month had the largest COGS?*/--------------------------------------------------
select month_name as month,
SUM(cogs) as total_cogs from sales_analysis
group by month_name
order by total_cogs desc;

-------------/*What product line had the largest revenue?*/------------------------------------
select product_line,
	sum(total) as total_revenue
from sales_analysis
group by product_line
order by total_revenue desc;

---------/*What is the city with the largest revenue?*/------------------------------------------
select city,
	sum(total) as total_revenue
from sales_analysis
group by city
order by total_revenue desc;

-------/*What product line had the largest VAT?*/-------------------------------------------------
select
	product_line,
    avg(tax_prcnt) as avg_tax
    from sales_analysis
    group by product_line
    order by avg_tax desc;
    
    ----/*Which branch sold more products than average product sold?*/-------------------------------
    select branch, sum(quantity) as qty
    from sales_analysis
    group by branch
    having sum(quantity) > (select avg(quantity) from sales_analysis);
    
    --/*What is the most common product line by gender?*/---------------------------------------------
    select gender, product_line,
		count(gender) as total_cnt
	from sales_analysis
    group by gender, product_line
    order by total_cnt desc;
    
    ---/*What is the average rating of each product line?*/---------------------------------------------
    select round(avg(rating), 2) as avg_rating,
     product_line
	from sales_analysis
    group by product_line
    order by avg_rating desc;
    
    --------------------------------------------------------------------------------
    -----------/*Sales*/-------------------------------------------------------------
    --/*Number of sales made in each time of the day per weekday?*/----------------------------------------
    select 
		time_of_day,
        count(*) as total_sales
	from sales_analysis
    where day_name = "Monday"
    group by time_of_day
    order by total_sales;
    
    -----/*Which of the customer types brings the most revenue?*/----------------------------------------
    select
		customer_type,
        sum(total) as total_rev
	from sales_analysis
    group by customer_type
    order by total_rev desc;
    
    ----/*Which city has the largest tax percent/ VAT (Value Added Tax)?*/-------------------------------
    select city, 
		avg(tax_prcnt) as vat
	from sales_analysis
    group by city
    order by vat desc;
	
    -------/*Which customer type pays the most in VAT?*/------------------------------------------------
    select customer_type,
		avg(tax_prcnt) as vat
	from sales_analysis
    group by customer_type
    order by vat desc;
    
    
    -------------------------------------------------------------------------------------------
    ---------------/*CUSTOMERS*/---------------------------------------------------------------
    --------/*How many unique customer types does the data have?*/-------------
    SELECT distinct customer_type from sales_analysis;
    
    -- How many unique payment methods does the data have? --
    SELECT DISTINCT payment from sales_analysis;
    
    -- What is the most common customer type? --
    select customer_type,
		count(*) from sales_analysis
	group by customer_type;
    
    -- Which customer type buys the most? --
    select customer_type,
		count(*) from sales_analysis
	group by customer_type;
    
    -- What is the gender of most of the customers? --
    select gender, count(gender) from sales_analysis
    group by gender;
    select gender, count(*) as gender_cnt
    from sales_analysis
    group by gender
    order by gender_cnt;
    
    -- What is the gender distribution per branch? --
    select gender, count(*) as gender_cnt
    from sales_analysis
    where branch = "C"
    group by gender
    order by gender_cnt;  
    
    -- Which time of the day do customers give most ratings? --
    SELECT time_of_day, avg(rating) as avg_rating
    from sales_analysis
    group by time_of_day
    order by avg_rating desc;
    
    -- Which time of the day do customers give most ratings per branch? --
    select time_of_day, avg(rating) as avg_rating
    from sales_analysis 
    where branch = "A"
    group by time_of_day
    order by  avg_rating;
    
    -- Which day fo the week has the best avg ratings? --
    select day_name, avg(rating) as avg_rating
    from sales_analysis
    group by day_name
    order by avg_rating desc;
    
    -- Which day of the week has the best average ratings per branch? --
    select day_name, avg(rating) as avg_rating
    from sales_analysis
    where branch = "c"
    group by day_name
    order by avg_rating desc;
