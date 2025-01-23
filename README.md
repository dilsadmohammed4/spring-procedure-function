# Spring Boot Product Management API

This project implements a Spring Boot application to manage product inventory, including updating stock quantities and calculating the total price of a product. Additionally, it integrates stored procedures and Swagger API documentation.

---

## Features

- **Update stock quantity**: Update the stock quantity of a specific product using a stored procedure.
- **Calculate total price**: Retrieve the total price of a product using a stored function.
- **Swagger Integration**: API documentation is available at `http://localhost:9191/swagger-ui/index.html`.

---

## Database Setup

### Create the `Product` Table

```sql
CREATE TABLE `product` (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    price DECIMAL(10,2) NOT NULL,
    stockQuantity INT NOT NULL
);
```

### Insert Sample Data

```sql
INSERT INTO `product` (name, price, stockQuantity) VALUES
('Laptop', 25000.0, 10),
('Smartphone', 5000.0, 12),
('Tablet', 12000.0, 8),
('Headphones', 2000.0, 15),
('Smartwatch', 8000.0, 20),
('Monitor', 10000.0, 5),
('Keyboard', 1500.0, 25),
('Mouse', 800.0, 30),
('Printer', 7000.0, 7),
('Router', 3500.0, 20);
```

### Stored Procedure: Update Stock Quantity

```sql
CREATE DEFINER=`root`@`localhost` PROCEDURE `update_stock`(
    IN productId INT,
    IN quantity INT
)
BEGIN
    UPDATE product
    SET stockQuantity = stockQuantity - quantity
    WHERE id = productId;
END;
```

### Stored Function: Get Total Price

```sql
CREATE DEFINER=`root`@`localhost` FUNCTION `get_total_price`(productId INT) RETURNS decimal(10,2)
    READS SQL DATA
    DETERMINISTIC
BEGIN
    DECLARE total DECIMAL(10,2);
    SELECT SUM(price * stockQuantity) INTO total
    FROM product
    WHERE id = productId;

    RETURN total;
END;
```

---

## API Endpoints

### Base URL

`http://localhost:9191/api/products`

### Endpoints

#### 1. Update Inventory Stock

- **URL**: `/update/{productId}/{quantity}`
- **Method**: `PUT`
- **Description**: Updates the stock quantity of a specific product.
- **Example**:
  ```bash
  curl -X PUT "http://localhost:9191/api/products/update/1/5"
  ```

#### 2. Get Total Price

- **URL**: `/total-price/{productId}`
- **Method**: `GET`
- **Description**: Retrieves the total price for a specific product.
- **Example**:
  ```bash
  curl -X GET "http://localhost:9191/api/products/total-price/1"
  ```

---

## Swagger Documentation

Swagger UI is available at:

[Swagger UI](http://localhost:9191/swagger-ui/index.html)

---

## How to Run

1. Clone the project.
2. Set up the database with the provided SQL scripts.
3. Run the Spring Boot application.
4. Access the API documentation at `http://localhost:9191/swagger-ui/index.html`.

---

## Dependencies

- Spring Boot
- Spring Data JPA
- MySQL
- springdoc-openapi-ui
- Lombok


### Why Use Stored Procedures and Functions?

Complex Logic: If you have complicated SQL operations — like multiple joins or conditional logic — stored procedures can help organize your code.

Performance: Stored procedures sometimes run faster because the database can optimize and cache them.

Security: You can permit to run a stored procedure without allowing direct access to the underlying tables.

Reusability: If many applications or parts of your code need the same SQL logic, you can store it in a procedure or function and call it whenever you want
