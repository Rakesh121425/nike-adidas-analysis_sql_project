# Nike_Adidas performance & customer_engagement SQL Project

![](https://github.com/Rakesh121425/nike-adidas-analysis_sql_project/blob/main/WhatsApp%20Image%202025-05-27%20at%2020.12.44_aeab6d6f.jpg)

## Project Objective
The primary objective of this project is to analyze and compare product performance of Nike and Adidas using SQL to derive meaningful marketing insights. By leveraging structured data (ratings, reviews, pricing, etc.), the project aims to:

Identify which brand has higher customer satisfaction.

Discover key product categories driving sales and engagement.

Evaluate pricing trends, review volume, and rating quality to guide marketing decisions.

Help the company optimize product positioning, branding, and consumer targeting strategies.

##QUERY SESSION
create table product (
Product_Name varchar(100),
Product_ID varchar(250),
Listing_Price int,
Sale_Price int,
Discount smallint,
Brand	varchar(50),
Description varchar(700),
Rating DECIMAL(2,1),
Review INT,
Last_Visited date

);

select * from product;

---project performance comparison and customer engagement---

---Brand Performance Comparison (Nike vs Adidas)----

---1. What is the total number of products listed for each brand?---

select brand,count(product_id) 
from product
group by brand;

----2. What is the average sale price, listing price, and discount percentage for each brand?---

select brand,
round(avg(sale_price),2) as avg_sales,
round(avg(Listing_Price),2) as avg_listing_price,
round(avg(discount),2) as discount
from product
group by brand;

----3. What is the average customer rating per brand?----

select brand,round(avg(rating),2)
from product
group by brand;

----4. What is the total number of reviews received by each brand?---

select brand,sum(review) from product
group by brand;


----5. What are the top 5 most-reviewed products for each brand?----

select product_name,review,
      row_number() over (partition by brand order by review desc)
	  from product
	  limit 5;

----6. Most discounted product for each brand:----


select product_name,discount,BRAND,
     ROW_NUMBER() over(partition by brand order by discount desc) as high_discount
from product;

----7. What is the average discount offered on products rated above 4 for each brand?--

SELECT product_name,round(AVG(DISCOUNT),2) as avg_discount
from product
where rating >=4
group by product_name;

-----8. What is the overall revenue potential (sum of sale prices) for each brand?-----

select brand,sum(sale_price) as revenue
from product
group by brand
order by revenue desc;

----9. Which products have the highest review-to-rating ratio (i.e., a lot of reviews but average ratings)?---

SELECT Product_Name, Review, Rating
FROM product
WHERE Review > 30 AND Rating < 3.5
ORDER BY Review DESC;

---10. Are products with higher discounts receiving more reviews or higher ratings?--

select discount,
round(avg(review),2) as avg_review,
round(avg(rating),2) as avg_rating
from product
group by discount
order by discount desc;

----11. How many products have ratings below 3.0? Which brand has more of them?---

select product_name,rating,brand,
row_number() over (partition by brand order by rating desc)
from product
where rating<3;

----12. What keywords are commonly found in descriptions of top-rated products?---

SELECT Product_Name, Rating, Description
FROM product
WHERE LOWER(Description) ILIKE '%COMFORT%' AND Rating > 4
ORDER BY Rating DESC;

SELECT Product_Name, Rating, Description
FROM product
WHERE LOWER(Description) ILIKE '%FEEL%' AND Rating > 4
ORDER BY Rating DESC;



---13. Are newer products (based on Last Visited date) receiving more reviews or better ratings?----

select last_visited,product_name,
round(avg(review)) as avg_reviews
from product
group by last_visited,product_name
order by last_visited desc;


----14. What’s the correlation between sale price and rating? Do premium products get better ratings?--


select brand,rating,
case
    when sale_price <5000 then '<5k'
	when sale_price between 5001 and 15000 then '5k_to_15k'
	when sale_price between 15001 and 25000 then '15k_to_25k'
	when sale_price between 25001 and 37000 then '25k_to_37k'
	end as price_catogery
from product
order by rating desc;


----Which brand (Nike or Adidas) has the highest average rating across all products, and how many reviews does that brand have in total?----

select brand, sum(review) as total_reviews,
round(avg(rating),2) as avg_rating,
row_number() over( order by avg(rating)  desc) as rank
from product
group by brand,review;

