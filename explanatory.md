# Beginner Explanatory Guide: DATA-203: Refactor Legacy Database Schema

> **Task Type**: Product Task  
> **Domain/Focus**: Database Schema Normalization

---

## 1. The Goal (In-Depth Beginner Explanation)

### The Core Problem
The task at hand involves refactoring a legacy database schema, specifically the `orders` table, which has become unwieldy and inefficient. Currently, this table contains over 50 columns, many of which hold redundant data. For example, customer information such as name, email, and phone number is repeated for every order, leading to unnecessary duplication. This not only wastes storage space but also complicates data management and retrieval. Additionally, the absence of indexes means that queries on this table are slow and inefficient, which can lead to poor performance in applications that rely on this data.

Fixing this issue is crucial for several reasons. First, a normalized database schema improves data integrity by reducing redundancy, making it easier to maintain and update records. Second, proper indexing on frequently queried columns enhances performance, allowing for faster data retrieval. Lastly, a well-structured database schema is essential for future scalability, ensuring that as the application grows, the database can handle increased loads without significant performance degradation.

### Jargon Buster (Key Terms Explained)
* **Normalization**: This is the process of organizing data in a database to reduce redundancy and improve data integrity. For example, instead of storing customer information in every order, we create a separate `customers` table and link it to the `orders` table using a unique customer ID.

* **Index**: An index is a database structure that improves the speed of data retrieval operations on a database table. Think of it like an index in a book; instead of reading every page to find a topic, you can quickly look it up in the index. For instance, adding an index on the `customer_id` column in the `orders` table allows the database to quickly locate all orders for a specific customer.

* **Denormalization**: This is the opposite of normalization, where data is intentionally duplicated across tables to improve read performance. While it can speed up data retrieval, it often leads to data anomalies and increased storage costs.

* **Foreign Key**: A foreign key is a field (or collection of fields) in one table that uniquely identifies a row of another table. It establishes a relationship between the two tables. For example, in our new `orders` table, the `customer_id` serves as a foreign key that links to the `customers` table.

### Expected Outcome
After implementing the solution, the database schema will be significantly improved. 

**Before**: The `orders` table contains redundant customer data, lacks indexes, and is difficult to maintain. Queries are slow due to the large number of columns and absence of proper indexing.

**After**: The database will have separate `customers`, `products`, `orders`, and `order_items` tables, each properly normalized. The `customers` table will store unique customer information, while the `orders` table will reference customers via a foreign key. Indexes will be added to frequently queried columns, resulting in faster query performance and easier data management.

---

## 2. Related Coding Concepts & Syntax (50% Theory, 50% Practice)

### Concept 1: Database Normalization
#### 📘 Theoretical Overview (50%)
Normalization is a fundamental concept in database design that aims to minimize redundancy and dependency by organizing fields and table relationships. The process typically involves dividing large tables into smaller, related tables and defining relationships between them. The most common forms of normalization are the first, second, and third normal forms (1NF, 2NF, 3NF), each with specific rules to follow.

If normalization is not applied, the database can suffer from various issues, such as data anomalies (insertion, update, and deletion anomalies), which can lead to inconsistent data. For example, if a customer's email changes, it would need to be updated in multiple places if stored redundantly, increasing the risk of errors.

#### 💻 Syntax & Practical Examples (50%)
* **Language Syntax**:
  ```sql
  CREATE TABLE customers (
      id INTEGER PRIMARY KEY AUTOINCREMENT,
      name TEXT NOT NULL,
      email TEXT NOT NULL UNIQUE,
      phone TEXT
  );
  ```
  - `CREATE TABLE`: This command is used to create a new table in the database.
  - `INTEGER PRIMARY KEY AUTOINCREMENT`: This defines a column that will automatically increment for each new record, ensuring a unique identifier.
  - `NOT NULL`: This constraint ensures that the column cannot have a NULL value.
  - `UNIQUE`: This constraint ensures that all values in the column are different.

* **Real-World Application**:
  ```sql
  INSERT INTO customers (name, email, phone) VALUES ('John Doe', 'john@example.com', '123-456-7890');
  ```
  - This command inserts a new customer into the `customers` table. The `name`, `email`, and `phone` fields are filled with specific values, ensuring that the customer data is stored in a normalized manner.

---

## 3. Step-by-Step Logic & Walkthrough

1. **Step 1: Locate and Analyze the Target File**
   * Navigate to the `src/` directory where the migration scripts are located. The primary file to focus on is `migration.py`.
   * Inspect the `migrate_schema` function, which is responsible for creating the new normalized tables.

2. **Step 2: Input Verification & Validation**
   * Before running the migration, ensure that the legacy `orders` table exists and contains data. This can be done by executing a simple SELECT query on the `legacy_orders` table.

3. **Step 3: Core Implementation / Modification**
   * In the `migrate_schema` function, ensure that the new tables (`customers`, `products`, `orders`, and `order_items`) are created correctly. Pay attention to the relationships, such as foreign keys linking `orders` to `customers`.
   * Modify the `migrate_data` function to ensure that data is correctly transferred from the `legacy_orders` table to the new tables without duplicating customer or product information.

4. **Step 4: Output Verification & Testing**
   * After implementing the changes, run the test suite in `test_migration.py` to verify that all tests pass. This will confirm that the new schema is functioning as expected and that data integrity is maintained.

---

## 4. Detailed Walkthrough of Test Cases

### Test Case 1: Standard / Success Case
* **Description**: This test checks that the `customers` table has a unique constraint on the email field.
* **Inputs**:
  ```json
  {
    "customers": [
      {"name": "Alice", "email": "alice@example.com", "phone": "111-222-3333"},
      {"name": "Bob", "email": "bob@example.com", "phone": "444-555-6666"}
    ]
  }
  ```
* **Step-by-Step Execution Trace**:
  1. The `migrate_schema` function is called, creating the `customers` table.
  2. The test checks the index list for the `customers` table.
  3. It verifies that there is a unique index on the `email` column.
* **Expected Output**: The test passes, confirming that the `customers.email` field has a UNIQUE constraint.

### Test Case 2: Edge Case / Validation Fail
* **Description**: This test checks that the `orders` table has a foreign key constraint linking to the `customers` table.
* **Inputs**:
  ```json
  {
    "orders": [
      {"customer_id": 1, "order_date": "2023-01-01", "status": "pending"},
      {"customer_id": 2, "order_date": "2023-01-02", "status": "completed"}
    ]
  }
  ```
* **Step-by-Step Execution Trace**:
  1. The `migrate_schema` function is executed, creating the `orders` table.
  2. The test checks for foreign keys in the `orders` table.
  3. If no foreign key exists linking to the `customers` table, the test fails.
* **Expected Output**: The test passes, confirming that the `orders` table has a foreign key constraint to the `customers` table, ensuring referential integrity.