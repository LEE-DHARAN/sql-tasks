
1.Create the Database and Tables

-- Create the database named ecommerce
CREATE DATABASE ecommerce;

-- Use the created ecommerce database
USE ecommerce;

-- Create the 'customers' table
CREATE TABLE customers (
    id INT AUTO_INCREMENT PRIMARY KEY,    
    name VARCHAR(100) NOT NULL,           
    email VARCHAR(100) NOT NULL UNIQUE,    
    address VARCHAR(255) NOT NULL          
);

-- Create the 'products' table
CREATE TABLE products (
    id INT AUTO_INCREMENT PRIMARY KEY,    
    name VARCHAR(100) NOT NULL,            
    price DECIMAL(10, 2) NOT NULL,         
    description TEXT                       
);

-- Create the 'orders' table
CREATE TABLE orders (
    id INT AUTO_INCREMENT PRIMARY KEY,    
    customer_id INT,                      
    order_date DATE NOT NULL,              

    total_amount DECIMAL(10, 2) NOT NULL,  
    FOREIGN KEY (customer_id) REFERENCES customers(id)  
);

-- Create a new table 'order_items' for order details
CREATE TABLE order_items (
    id INT AUTO_INCREMENT PRIMARY KEY,    
    order_id INT,                          
    product_id INT,                        
    quantity INT NOT NULL,                 
    price DECIMAL(10, 2) NOT NULL,         
    FOREIGN KEY (order_id) REFERENCES orders(id),  
    FOREIGN KEY (product_id) REFERENCES products(id)  
);

2.Insert Sample Data

-- Insert sample data into 'customers' table
INSERT INTO customers (name, email, address) VALUES 
('arun', 'arun@gmail.com', '2 Main St, chennai'),
('muthu', 'muthu@gmail.com', '4 main St, mumbai'),
('raanav', 'raanav@gmail.com', '7 main St, kochi');

-- Insert sample data into 'products' table
INSERT INTO products (name, price, description) VALUES 
('Product A', 30.00, 'Description of Product A'),
('Product B', 20.00, 'Description of Product B'),
('Product C', 50.00, 'Description of Product C'),
('Product D', 25.00, 'Description of Product D');


-- Insert sample data into 'orders' table
INSERT INTO orders (customer_id, order_date, total_amount) VALUES 
(1, '2024-12-01', 80.00),
(2, '2024-11-15', 150.00),
(3, '2024-12-10', 120.00);

-- Insert sample data into 'order_items' table
INSERT INTO order_items (order_id, product_id, quantity, price) VALUES 
(1, 1, 2, 30.00),  
(1, 2, 1, 20.00),  
(2, 3, 3, 50.00),  
(3, 4, 2, 25.00);

SQL Queries

1. Retrieve all customers who have placed an order in the last 30 days.
-- Get customers who have placed orders in the last 30 days
SELECT DISTINCT c.name, c.email
FROM customers c
JOIN orders o ON c.id = o.customer_id
WHERE o.order_date >= CURDATE() - INTERVAL 30 DAY;

2. Get the total amount of all orders placed by each customer.
-- Get the total amount spent by each customer
SELECT c.name, SUM(o.total_amount) AS total_spent
FROM customers c
JOIN orders o ON c.id = o.customer_id
GROUP BY c.id;

3. Update the price of Product C to 45.00.
-- Update the price of Product C
UPDATE products
SET price = 45.00
WHERE name = 'Product C';

4. Add a new column discount to the products table.
-- Add a discount column to the products table
ALTER TABLE products
ADD COLUMN discount DECIMAL(5, 2) DEFAULT 0.00;

5. Retrieve the top 3 products with the highest price.
-- Retrieve the top 3 products with the highest price
SELECT name, price
FROM products
ORDER BY price DESC
LIMIT 3;

6. Get the names of customers who have ordered Product A.
-- Get the names of customers who ordered Product A
SELECT DISTINCT c.name
FROM customers c
JOIN orders o ON c.id = o.customer_id
JOIN order_items oi ON o.id = oi.order_id
JOIN products p ON oi.product_id = p.id
WHERE p.name = 'Product A';

7. Join the orders and customers tables to retrieve the customer's name and order date for each order.
-- Join orders and customers to get customer name and order date

SELECT c.name, o.order_date
FROM orders o
JOIN customers c ON o.customer_id = c.id;

8. Retrieve the orders with a total amount greater than 150.00.

-- Retrieve orders with a total amount greater than 150.00
SELECT * FROM orders
WHERE total_amount > 150.00;

9. Normalize the database by creating a separate table for order_items and updating the orders table to reference the order_items table.

-- Normalize the database by creating 'order_items' and linking it to 'orders'
-- This step was already done by creating the 'order_items' table in Step 1.

-- You might need to modify existing orders and remove product details from the 'orders' table, if present.

  1.Create the order_items Table

   -- Create 'order_items' table for order details (normalized structure)
   CREATE TABLE order_items (
    id INT AUTO_INCREMENT PRIMARY KEY,   -- Unique item ID
    order_id INT,                        -- Foreign key referencing 'orders'
    product_id INT,                      -- Foreign key referencing 'products'
    quantity INT NOT NULL,               -- Quantity of the product ordered
    price DECIMAL(10, 2) NOT NULL,       -- Price of the product at the time of the order
    FOREIGN KEY (order_id) REFERENCES orders(id) ON DELETE CASCADE,   -- Foreign key to 'orders'
    FOREIGN KEY (product_id) REFERENCES products(id) ON DELETE CASCADE   -- Foreign key to 'products'
);

  2.Update the orders Table

   -- Drop columns related to product details from the 'orders' table (if they exist)
      ALTER TABLE orders
      DROP COLUMN product_id,
      DROP COLUMN quantity;

  3.Inserting Data into order_items

   -- Insert order items into the 'order_items' table based on existing orders
   INSERT INTO order_items (order_id, product_id, quantity, price)
   SELECT o.id, oi.product_id, oi.quantity, oi.price
   FROM orders o
   JOIN order_items oi ON o.id = oi.order_id;

10. Retrieve the average total of all orders.

-- Get the average total of all orders
SELECT AVG(total_amount) AS average_order_amount
FROM orders;
