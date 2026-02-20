# RetailStream-SQL-Analysis
-- Create Customers Table
CREATE TABLE Customers (
    customer_id SERIAL PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    signup_date DATE DEFAULT CURRENT_DATE
);

-- Create Products Table
CREATE TABLE Products (
    product_id SERIAL PRIMARY KEY,
    product_name VARCHAR(100) NOT NULL,
    category VARCHAR(50),
    price DECIMAL(10, 2) NOT NULL,
    stock_count INT DEFAULT 0
);

-- Create Orders Table
CREATE TABLE Orders (
    order_id SERIAL PRIMARY KEY,
    customer_id INT REFERENCES Customers(customer_id),
    order_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    total_value DECIMAL(10, 2)
);

-- Create Order Line Items
CREATE TABLE Order_Items (
    line_item_id SERIAL PRIMARY KEY,
    order_id INT REFERENCES Orders(order_id),
    product_id INT REFERENCES Products(product_id),
    quantity INT NOT NULL,
    price_at_sale DECIMAL(10, 2) NOT NULL
);
A) Revenue by Category
Question: Which product categories are driving the most revenue?

SQL
SELECT p.category, 
       SUM(oi.quantity * oi.price_at_sale) AS total_revenue
FROM Products p
JOIN Order_Items oi ON p.product_id = oi.product_id
GROUP BY p.category
ORDER BY total_revenue DESC;
B) Customer Lifetime Value (CLV)
Question: Who are our top 1% of customers based on total spending?

SQL
SELECT c.first_name, c.last_name, 
       COUNT(o.order_id) AS total_orders,
       SUM(o.total_value) AS total_spent
FROM Customers c
JOIN Orders o ON c.customer_id = o.customer_id
GROUP BY c.customer_id
ORDER BY total_spent DESC
LIMIT 10;
C) Low Stock Alert
Question: Which items have less than 10 units left?

SQL
SELECT product_name, stock_count
FROM Products
WHERE stock_count < 10
ORDER BY stock_count ASC;
