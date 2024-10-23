# Amazon_SQL_Project
# Amazon E-Commerce Dataset

## Description:
This Dataset contains synthetic data simulating an e-commerce platform with India-specific data. 
It had 10,000 sales records and 9 tables on customers, sellers, products, orders, payments, shipping, and inventory.
## Objective:
### Level: Advanced SQL Project.
The main objective of this project is to showcase my SQL skills at an advanced level by addressing real-world business problems. 
All the advanced-level concepts like joins, subqueries, windows function, cte, and procedures are used to solve business problems. 
The analysis covers sales trends, customer behavior, product performance, inventory management, payment, and shipment analysis.
### Dataset Files
The following CSV files are included in this dataset:
1.	customers.csv - Contains customer information.
2.	sellers.csv - Contains seller information.
3.	categories.csv - Contains product categories.
4.	products.csv - Contains product details.
5.	inventory.csv - Contains stock information for each product.
6.	orders.csv - Contains information about customer orders.
7.	order_items.csv - Contains details of items in each order.
8.	payments.csv - Contains payment information for each order.
9.	shipping.csv - Contains shipping details for each order.
## Tables:
### 1. customers.csv
Column	Data Type	Description
customer_id	INT	Unique ID for each customer
first_name	VARCHAR	Customer's first name
last_name	VARCHAR	Customer's last name
state	VARCHAR	Customer's state of residence
city	VARCHAR	Customer's city of residence
created_at	DATETIME	Account creation date

### 2. sellers.csv
Column	Data Type	Description
seller_id	INT	Unique ID for each seller
seller_name	VARCHAR	Seller's company name
seller_city	VARCHAR	

### 3. categories.csv
Column	Data Type	Description
category_id	INT	Unique ID for each category
category_name	VARCHAR	Name of the product category

### 4. products.csv
Column	Data Type	Description
product_id	INT	Unique ID for each product
name	VARCHAR	Product name
cogs	FLOAT	Price of the product (INR)
category_id	INT	Foreign Key to Categories table
seller_id	INT	Foreign Key to Sellers table

### 5. inventory.csv
Column	Data Type	Description
inventory_id	INT	Unique ID for each inventory record
product_id	INT	Foreign Key to Products table
stock	INT	Number of units in stock
last_updated	DATETIME	Last stock update date

### 6. orders.csv
Column	Data Type	Description
order_id	INT	Unique ID for each order
customer_id	INT	Foreign Key to Customers table
order_date	DATETIME	Date of order placement
payment_id	INT	Foreign Key to Payments table
shipping_id	INT	Foreign Key to Shipping table
total_amount	FLOAT	Total amount of the order (INR)

### 7. order_items.csv
Column	Data Type	Description
order_item_id	INT	Unique ID for each order item
order_id	INT	Foreign Key to Orders table
product_id	INT	Foreign Key to Products table
quantity	INT	Number of units ordered
price	FLOAT	Price per unit of the product

### 8. payments.csv
Column	Data Type	Description
payment_id	INT	Unique ID for each payment
payment_type	VARCHAR	Payment method (Credit, UPI, etc.)
payment_date	DATETIME	Date of payment
amount	FLOAT	Payment amount (INR)
payment_status		
	VARCHAR(50)	Status of the payment (e.g., 'Completed', 'Failed', 'Refunded')

### 9. shipping.csv
Column	Data Type	Description
shipping_id	INT	Unique ID for each shipment
shipping_date	DATETIME	Date when the order was shipped
delivery_date	DATETIME	Estimated or actual delivery date
Shipping_status	VARCHAR	Status of the shipping(e.g., ‘’)

Entity Relationship Diagram:

 
Database creation:
CREATE DATABASE AmazonDatabase;
USE AmazonDatabase;

###  Creating all tables and importing the data from csv file. 
```
CREATE TABLE Categories (
categorie_id INT PRIMARY KEY,
categorie_name VARCHAR(50) NOT NULL
);
SELECT * FROM Categories;

CREATE TABLE Sellers(
seller_id INT PRIMARY KEY,
seller_name VARCHAR(50) NOT NULL,
seller_city VARCHAR(50) NOT NULL
);
SELECT * FROM Sellers;

CREATE TABLE Customers(
customer_id INT PRIMARY KEY,
First_name VARCHAR(50) NOT NULL,
Last_name  VARCHAR(50) NOT NULL,
State VARCHAR(50) NOT NULL,
city VARCHAR(50) NOT NULL,
created_at DATETIME NOT NULL
);
SELECT * FROM customers;

CREATE TABLE Products(
product_id INT PRIMARY KEY,
name VARCHAR(50) NOT NULL,
price FLOAT NOT NULL,
categorie_id INT NOT NULL,
seller_id INT NOT NULL,                            
FOREIGN KEY (categorie_id) REFERENCES categories(categorie_id), 
FOREIGN KEY (seller_id) REFERENCES sellers(seller_id) 
);
SELECT * FROM Products;

CREATE TABLE Inventory(
inventory_id INT PRIMARY KEY,
product_id INT NOT NULL,
stock INT NOT NULL,
last_updated DATETIME,
FOREIGN KEY (product_id) REFERENCES Products(product_id)
);
SELECT * FROM Inventory;

CREATE TABLE Payments(
payment_id INT PRIMARY KEY,
payment_type VARCHAR(50) NOT NULL,
payment_date DATETIME NOT NULL,
amount INT NOT NULL,
payment_status VARCHAR(50) NOT NULL
);
SELECT * FROM Payments;

CREATE TABLE Shipping(
shipping_id INT PRIMARY KEY,
shipping_date DATETIME NOT NULL,
delivery_date DATETIME NOT NULL,
shipping_status VARCHAR(50)
);
SELECT * FROM Shipping;

CREATE TABLE Orders(
order_id	INT PRIMARY KEY,
customer_id	INT NOT NULL,
order_date	DATETIME NOT NULL,
payment_id	INT NOT NULL,
shipping_id	INT NOT NULL,
total_amount FLOAT NOT NULL,
FOREIGN KEY (customer_id) REFERENCES Customers(customer_id),
FOREIGN KEY (payment_id) REFERENCES Payments(payment_id),
FOREIGN KEY (shipping_id) REFERENCES Shipping(shipping_id)
);
SELECT * FROM Orders;

CREATE TABLE Order_items(
order_item_id	INT PRIMARY KEY,
order_id	INT NOT NULL,
product_id	INT NOT NULL,
quantity	INT NOT NULL,
price	FLOAT NOT NULL,
FOREIGN KEY (product_id) REFERENCES Products(product_id),
FOREIGN KEY (order_id) REFERENCES Orders(order_id)
);
```
### ER Diagaram:
![](https://github.com/BhavanaBalasa/Amazon_SQL_Project/blob/main/Amazon_ER.png)
### Business Problems:
   Based on my understanding, I have identified several issues, and the information obtained through these queries, which range from basic to advanced, and use this information to improve future sales forecasting and business planning.

 ### 1. Total Revenue generated
```
SELECT concat(round(SUM(total_amount)/1000000,2),'M') TotalRevenue 
FROM Orders
INNER JOIN Payments on Orders.payment_id=Payments.payment_id
where Payment_status = 'Completed';
```
 ### 2. Analyzing the sales trend over the past few years, and identifying patterns of growth or decline.
```
SELECT YEAR(Orders.order_date) YEARNUM, concat(round(SUM(total_amount)/1000,2),'k') sales
FROM Orders
INNER JOIN Payments on Orders.payment_id=Payments.payment_id
where Payment_status = 'Completed'
GROUP BY YEARNUM
ORDER BY YEARNUM;
```
 ### 3. Total no of orders placed successfully with no payment failed and refunds. 
```
SELECT count(*) OrdersCount
FROM Orders
INNER JOIN Payments on Orders.payment_id=Payments.payment_id
where Payment_status = 'Completed';
```
### 4. Get count of products for each category top 5.
```
SELECT categories.categorie_name, COUNT(product_id) Productcount 
FROM Products
INNER JOIN Categories on Products.Categorie_id=Categories.categorie_id
GROUP BY categories.categorie_name
ORDER BY Productcount DESC
LIMIT 5;
```
### 5. Calculate the most profitable product category.
```
SELECT categories.categorie_name, SUM(Order_items.quantity*Order_items.price) AS Total_Amount
FROM Categories
INNER JOIN Products on Categories.Categorie_id=Products.categorie_id
INNER JOIN Order_items on Products.product_id=Order_items.product_id
GROUP BY categories.categorie_name
ORDER BY Total_Amount DESC
LIMIT 10;
```
### 6. Top 5 states with high revenue generation.
```
SELECT Customers.state, round(SUM(orders.total_amount),2) Total_Revenue
FROM Customers
INNER JOIN Orders ON Orders.customer_id=Customers.customer_id
INNER JOIN Payments ON Orders.payment_id=Payments.payment_id
WHERE Payments.payment_status != "Refunded"
GROUP BY Customers.State
ORDER BY Total_Revenue DESC
LIMIT 5;
```
### 7. From which States the products are highly in returned.
```
SELECT Customers.state, COUNT(shipping.shipping_status) Returned_count
FROM Customers
INNER JOIN Orders ON Customers.customer_id=Orders.customer_id
INNER JOIN Shipping ON Orders.shipping_id=Shipping.shipping_id
WHERE Shipping.shipping_status= "Returned"
GROUP BY Customers.state
ORDER BY Returned_count DESC
LIMIT 4;
```
### 8. List all products with low inventory (stock less than 20).
```
SELECT products.name, inventory.stock
FROM products
JOIN inventory ON products.product_id = inventory.product_id
WHERE inventory.stock < 20
ORDER BY inventory.stock;
```
### 9. Compute the average order value for each customer. Include customers with more than 5 orders.
```
SELECT Customers.customer_id, Customers.First_name, AVG(Orders.total_amount)/COUNT(Orders.order_id) AOV 
FROM Customers
INNER JOIN Orders ON Customers.customer_id=Orders.customer_id
GROUP BY Customers.customer_id, Customers.First_name
HAVING COUNT(Orders.order_id) > 5
ORDER BY AOV DESC;
```
### 10. Query monthly total sales for last year.
```
SELECT MONTH(Orders.order_date) MonthNO, YEAR(Orders.order_date) YearNO, ROUND(SUM(total_amount),2) Total_Sales
FROM Orders
GROUP BY MonthNO, YearNO 
HAVING YearNO=2023
ORDER BY MonthNO, YearNO;
```
### 12. Find the orders with delays in delivery where the date is 7 days after the shipping date. Include customer, order details.
```
WITH delay_days_calcu AS(
SELECT customers.first_name, orders.order_id, orders.order_date, shipping.shipping_date, datediff(shipping.shipping_date,orders.order_date) delay_days
FROM orders
INNER JOIN customers ON customers.customer_id=orders.customer_id
INNER JOIN shipping ON orders.shipping_id=shipping.shipping_id
WHERE shipping.shipping_status='Shipped')
SELECT * FROM delay_days_calcu
WHERE delay_days>7
ORDER BY delay_days;
```
### 13. Calculate the percentage of successful payments across all orders. Include other .
```
SELECT Payments.payment_status, COUNT(*) Total_payments, COUNT(*)/(SELECT COUNT(*) FROM payments) *100 Percentage
FROM Payments
INNER JOIN Orders ON Payments.payment_id=orders.payment_id
GROUP BY Payments.payment_status;
```
### 14. Find the top 5 sellers based on total value.
```
SELECT Sellers.seller_id, sellers.seller_name, SUM(order_items.quantity*order_items.price) total_value
FROM sellers
INNER JOIN Products ON sellers.seller_id=Products.seller_id
INNER JOIN order_items ON order_items.product_id=Products.product_id
GROUP BY Sellers.seller_id, sellers.seller_name
ORDER BY total_value DESC
LIMIT 5;
```
### 15. Calculate the profit margin for each product (difference between products and cost of goods sold). Rank produced by their profit margin, showing highest to lowest.
```
SELECT products.product_id, products.name, (SUM(order_items.price-products.price)/(SELECT SUM(order_items.price) FROM order_items)) * 100 PROFIT_MARGIN,
DENSE_RANK() OVER(ORDER BY (SUM(order_items.price-products.price)/(SELECT SUM(order_items.price) FROM order_items)) * 100 DESC) PRODUCT_RANK
FROM Products
INNER JOIN order_items ON Products.product_id=order_items.product_id
GROUP BY products.product_id, products.name;
```
 ### 16. Query top 10 product by their return number.
```
SELECT products.product_id, products.name, count(*) return_count
FROM products
INNER JOIN order_items ON Products.product_id=order_items.product_id
INNER JOIN orders ON order_items.order_id=orders.order_id
INNER JOIN shipping ON orders.shipping_id=shipping.shipping_id
where shipping_status='Returned'
GROUP BY products.product_id, products.name
ORDER BY return_count DESC
LIMIT 10;
```
### 17. Query the distribution of orders placed by hour of a day. Create a time-based analysis to understand the peak time. 
```
SELECT HOUR(order_date) hour_number, COUNT(*) orders_count FROM orders
GROUP BY hour_number
ORDER BY orders_count DESC;
```
 ### 18. Query top 5 customers with the highest no of orders for each state. 
```
WITH customer_rank AS(
SELECT customers.customer_id, first_name, state , count(orders.order_id) orders_count, DENSE_RANK() OVER(PARTITION BY  state ORDER BY count(orders.order_id) DESC  ) RANK_CUSTOMER
FROM customers
INNER JOIN orders ON customers.customer_id=orders.customer_id
GROUP BY customers.customer_id, first_name, state
)
SELECT * FROM customer_rank
WHERE RANK_CUSTOMER <=5;
```
### 19. Calculate the total revenue handled by each seller. Include the total no of orders handled and the average delivery time for each provider. 
```
SELECT sellers.seller_id, sellers.seller_name, SUM(order_items.price) total_revenue, count(order_items.order_item_id) no_of_orders, 
abs(avg(datediff(orders.order_date,shipping.shipping_date))) avg_delivery_time
FROM sellers
INNER JOIN products ON sellers.seller_id=products.seller_id
INNER JOIN order_items ON products.product_id=order_items.product_id
INNER JOIN orders ON order_items.order_id=orders.order_id
INNER JOIN shipping ON orders.shipping_id=shipping.shipping_id
GROUP BY sellers.seller_id, sellers.seller_name
ORDER BY sellers.seller_name;
```
### 20. Create a Stored Procedure that the same quantity should be reduced from the inventory table as soon as the product is sold. After adding any sales records it should update the stock in the inventory table and the product and quantity purchased.  
```
DELIMITER $$
CREATE PROCEDURE Update_Inventory(IN c_product_id INT, IN c_quantity INT)
BEGIN
DECLARE stockValue INT;
SELECT stock INTO stockValue 
FROM inventory
WHERE product_id=c_product_id;

IF stockValue > c_quantity THEN
   UPDATE inventory 
   SET stock=stock-c_quantity, last_updated= now()
   WHERE product_id=c_product_id;
    SELECT 'Inventory updated successfully';
ELSE
	SELECT 'Insufficient stock' AS message;
    END IF;
END $$
DELIMITER ;

CALL Update_Inventory(669,100)
```
### Insights:
-	The decrease in sales by 24% from 2020 to 2023.
-	The payment issues are as the failed rate is 33.3%.
-	In Inventory more than 15% of products are less in stock and need to be ordered and updated.
  
### Learning Outcomes:
-	Design and implement the database schema.
-	Cleaning and analyzing the dataset.
-	Understanding the business work flow.
-	Implementing advanced-level concepts to solve business problems and error handling.
-	Created a stored procedure to update the data base.




