# 🛒 ECommerce Database Task

This project sets up a simple eCommerce database using MySQL with three main tables: `customers`, `orders`, and `products`. The queries include table creation, schema alteration, and common operations like retrieving customer info, updating products, and normalizing the database.

---
**📋 Table Structures**

1️⃣ Customers Table

Stores information about each customer.

CREATE TABLE customers (
    id INT AUTO_INCREMENT PRIMARY KEY,      
    name VARCHAR(100) NOT NULL,            
    email VARCHAR(100) NOT NULL UNIQUE,     
    address TEXT                            
);

2️⃣ Orders Table

Stores each order made by customers.

CREATE TABLE orders (
    id INT AUTO_INCREMENT PRIMARY KEY,     
    customer_id INT,                        
    order_date DATE,                       
    total_amount INT                        
);

Add foreign key for customer:

ALTER TABLE orders 
ADD FOREIGN KEY (customer_id) 
REFERENCES customers(id) 
ON DELETE RESTRICT ON UPDATE RESTRICT;

3️⃣ Products Table

Stores the available products in the store.

CREATE TABLE products (
    id INT AUTO_INCREMENT PRIMARY KEY,      
    name VARCHAR(30),                       
    price INT,                              
    description VARCHAR(100)  ); 

---    
**🔍 SQL Query Tasks & Explanations**

1. Customers who ordered in the last 30 days
   
  SELECT customers.name 
FROM customers 
LEFT JOIN orders ON customers.id = orders.customer_id 
WHERE DATE_SUB(CURDATE(), INTERVAL 30 DAY) <= orders.order_date;

// Retrieves names of customers who placed orders within the last 30 days.
---
2. Total amount of orders by each customer
   
   SELECT customer_id, SUM(total_amount) AS total 
FROM orders 
GROUP BY customer_id;

//Calculates total order value per customer.
---
3. Update the price of Product C to 45

    UPDATE products 
SET price = 45 
WHERE name = 'C';

//Updates the price of Product C.
---
4. Add a discount column to products

   ALTER TABLE products 
ADD discount INT;

//Adds a new column to store discount values.
---
5. Top 3 most expensive products

   SELECT * 
FROM products 
ORDER BY price DESC 
LIMIT 3;

// Retrieves top 3 products with the highest price.
---
6. Customers who ordered Product A

Step 1: Add product_id to orders table

    ALTER TABLE orders 
ADD COLUMN product_id INT UNSIGNED;

Step 2: Add foreign key constraint

ALTER TABLE orders 
ADD CONSTRAINT fk_product 
FOREIGN KEY (product_id) REFERENCES products(id);

Step 3: Query customers

SELECT customers.name 
FROM customers 
JOIN orders ON orders.customer_id = customers.id 
JOIN products ON orders.product_id = products.id 
WHERE products.name = 'A';

//Lists customers who ordered Product A.
---
7. Join orders and customers to show order dates

   SELECT customers.name, orders.order_date 
FROM customers 
JOIN orders ON customers.id = orders.customer_id;

//Displays each customer's name with their order date.
---
8. Orders above ₹150

   SELECT * 
FROM orders 
WHERE total_amount > 150;

// Retrieves orders with a total greater than 150.
---
9. Normalize using order_items table

    Step 1: Create order_items table
   
   CREATE TABLE order_items (
    id INT UNSIGNED NOT NULL AUTO_INCREMENT,
    order_id INT UNSIGNED NOT NULL,
    product_id INT UNSIGNED NOT NULL,
    quantity INT UNSIGNED NOT NULL,
    price DECIMAL NOT NULL,
    PRIMARY KEY (id)
);

Step 2: Add foreign keys

ALTER TABLE order_items 
ADD FOREIGN KEY (product_id) REFERENCES products(id) 
ON DELETE RESTRICT ON UPDATE RESTRICT;

ALTER TABLE order_items 
ADD FOREIGN KEY (order_id) REFERENCES orders(id) 
ON DELETE RESTRICT ON UPDATE RESTRICT;

//Splits orders into items to handle multiple products per order (normalization).
Example: View products in order ID 1
SELECT * 
FROM order_items 
JOIN products ON order_items.product_id = products.id 
WHERE order_items.order_id = 1;
---
🔟 Average total of all orders

  SELECT ROUND(AVG(total_amount)) AS Average 
FROM orders;

//Returns the average order total.
---

