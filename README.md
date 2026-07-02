**use new_project;


-- 1. Year-over-Year (YoY) Sales & Profit Growth

select	 year , round(sum(s.sales),2) as total_sales,
round(sum(s.profit),2) as total_profit
from sales s
join calender_file c
on s.orderdate = c.date 
group by c.year  
order by c.year;


-- 2. Seasonality (The Weekend Effect)

select c.Day_Name,
round(sum(s.sales),2) as total_sales,
sum(case when s.returned = 'yes' then 1 else 0 end ) as total_returns
from sales s 
join calender_file c 
on s.OrderDate = c.Date
group by c.day_name
order by total_sales desc;


-- 3. The Best Quarter

select c.Year,
	   c.`Quarter(Q)`,
       round(sum(s.sales),2) as total_sales
from sales s 
join Calender_file c 
on s.OrderDate = c.date
group by c.Year,
	   c.`Quarter(Q)`
       order by total_sales
       desc limit 1;

       
-- 4. The Discount Trap

 select category, `sub-category`,
                round(sum(sales),2) as total_sales,
                round(avg(Discount)*100,2) as avg_discount_pct,
                round(sum(profit),2) as total_profit
from Sales
group by Category, `Sub-Category` 
order by total_profit asc;   


-- 5. The Return Problem

  select region, count(*) as total_orders,
           sum(case when Returned = 'Yes' then 1 else 0 end) as returned_orders,
		   round((sum(case when Returned = 'yes' then 1 else  0 end) / count(*)) * 100,2)
           as return_rate_pct
 from sales
 group by region 
 order by return_rate_pct desc;


-- 6. Top 10 Loss-Making Customers		

select Customer_ID, Customer_Name,
     round(sum(sales),2) as total_sales,  
     round(sum(Profit),2) as total_loss
     from sales 
     group by Customer_ID, Customer_Name 
     having total_loss <0 
     order by total_loss asc
     limit 10;

     
-- 7. Shipping Delay Analysis   

  select 
         Ship_Mode, 
         round(avg(datediff(ShipDate,OrderDate)),2) as avg_shipping_days
         from sales 
         group by Ship_Mode 
         order by avg_shipping_days; 

         
-- 8. Late Delivery Impact

with shipingdata as (
select order_id, returned,
datediff(ShipDate, OrderDate)  as delivery_days
from sales )

select 
     case when delivery_days > 3 then 'late ( >3 days )' 
     else 'on time (<= 3 Days)' end as delivery_status,
     count(*) as total_orders, 
     sum(case when Returned = 'Yes' then 1 else 0 end) as returned_orders,
     round((sum(case when Returned = 'Yes' then 1 else 0 end) / count(*)) * 100,2)
     as retuned_rate_pct
     from shipingdata 
     group by delivery_status;

     
-- 9. Salesperson Performance

 select 
		Retail_Sales_People,
        round(sum(sales),2) as total_sales, 
        round(sum(profit),2) as total_profit,
        round((sum(profit) /sum(sales)) *100,2) as profit_margin_pct
        from sales
        group by Retail_Sales_People
        order by total_sales desc;
        
        
     
     
     
     
     **
