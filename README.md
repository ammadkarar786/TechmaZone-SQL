
## 🟢 Module 1: The Foundation – Understanding Data Types
**Goal:** Before we build a table, we must understand what "kind" of data lives in it. In Data Science, choosing the wrong type leads to storage waste or calculation errors.

### Step 1.1: Why Data Types Matter?
*   **Data Engineering View:** Wrong types break ETL pipelines (e.g., trying to do math on text).
*   **Data Science View:** `FLOAT` might lose precision in financial models; `DECIMAL` is safer.

### Step 1.2: Common Data Types Cheat Sheet
| Type | Use Case | Example |
|------|----------|---------|
| `INT` | Whole numbers (IDs, Counts) | `customer_id = 101` |
| `DECIMAL(p, s)` | Exact money/precision | `price = 99.99` |
| `VARCHAR(n)` | Text with variable length | `name = 'Ammad'` |
| `DATE` | Calendar dates | `2026-06-13` |
| `TIMESTAMP` | Date + Time (for logs) | `2026-06-13 14:30:00` |
| `BOOLEAN` | True/False flags | `is_active = TRUE` |

---

## 🔵 Module 2: The Skeleton – CREATE TABLE
**Goal:** Learn the basic syntax to create an empty container for our data.

### Step 2.1: Basic Syntax
```sql
CREATE TABLE table_name (
    column_name DATA_TYPE,
    column_name DATA_TYPE
);
```

### Step 2.2: Practical Task – The "Raw" Table
Let's create a simple table for **Customer Feedback** without any rules yet.

```sql
CREATE TABLE feedback_raw (
    id INT,
    customer_name VARCHAR(50),
    rating INT,
    comment TEXT,
    submission_date DATE
);
```

**👨‍🏫 Instructor Note:** Ask students: *"What could go wrong with this table?"*  
*(Expected answers: We can have duplicate IDs, missing names, or a rating of 1000.)*

---

## 🟠 Module 3: The Rules – Adding Constraints
**Goal:** Enforce data quality at the database level. This is crucial for **Data Cleaning** later. If the data is clean when it enters, your analysis is easier.

### Step 3.1: NOT NULL (The "Must-Have" Rule)
**Why?** In Data Science, missing values (`NULL`) require imputation. It’s better to prevent them if the data is critical.

```sql
ALTER TABLE feedback_raw 
MODIFY COLUMN customer_name VARCHAR(50) NOT NULL;
```

### Step 3.2: UNIQUE (The "No-Duplicates" Rule)
**Why?** Prevents double-counting in analytics. Every customer should have one unique email.

```sql
ALTER TABLE feedback_raw 
ADD CONSTRAINT uk_feedback_id UNIQUE (id);
```

### Step 3.3: CHECK (The "Business Logic" Rule)
**Why?** Ensures data makes sense. A rating cannot be negative or above 5.

```sql
ALTER TABLE feedback_raw 
ADD CONSTRAINT chk_rating CHECK (rating BETWEEN 1 AND 5);
```

### Step 3.4: DEFAULT (The "Auto-Fill" Rule)
**Why?** Saves time and ensures consistency for non-critical fields.

```sql
ALTER TABLE feedback_raw 
MODIFY COLUMN submission_date DATE DEFAULT CURRENT_DATE;
```

---

## 🟣 Module 4: The Connections – Primary & Foreign Keys
**Goal:** Link tables together. This is the heart of **Relational Databases** used in ERP systems like Oracle Fusion.

### Step 4.1: Primary Key (PK)
**Definition:** The unique fingerprint of a row.
**Action:** Let's make `id` the official Primary Key.

```sql
ALTER TABLE feedback_raw 
ADD PRIMARY KEY (id);
```

### Step 4.2: Foreign Key (FK)
**Scenario:** We have a `customers` table and a `feedback` table. We want to ensure feedback only comes from *existing* customers.

1.  **Create the Parent Table:**
    ```sql
    CREATE TABLE customers (
        cust_id INT PRIMARY KEY,
        email VARCHAR(100) UNIQUE
    );
    ```

2.  **Add the Link to Feedback Table:**
    ```sql
    -- First, add the column to hold the ID
    ALTER TABLE feedback_raw 
    ADD COLUMN cust_id INT;

    -- Now, create the relationship
    ALTER TABLE feedback_raw 
    ADD CONSTRAINT fk_customer 
    FOREIGN KEY (cust_id) REFERENCES customers(cust_id);
    ```

**👨‍🏫 Discussion:** *What happens if I try to insert feedback for a `cust_id` that doesn't exist in the `customers` table?* (Answer: The database will reject it.)

---

## 🔴 Module 5: The Cleanup – TRUNCATE, DROP, and "REMOVE"
**Goal:** Understand how to delete data vs. delete structures.

### Step 5.1: Clarifying "REMOVE"
**Important:** There is no `REMOVE` command in standard SQL. Students often confuse this with:
*   `DELETE` (DML - removes rows one by one)
*   `DROP` (DDL - removes the whole object)

### Step 5.2: TRUNCATE (The "Fast Wipe")
**Use Case:** You want to clear all data for a fresh start but keep the table structure and constraints.
**Speed:** Much faster than `DELETE` because it doesn't log every row deletion.

```sql
TRUNCATE TABLE feedback_raw;
-- Result: Table is empty, but columns and keys still exist.
```

### Step 5.3: DROP (The "Nuclear Option")
**Use Case:** The table is no longer needed, or you made a mistake in the design and want to start over.
**Warning:** This deletes data AND structure. It is irreversible.

```sql
DROP TABLE feedback_raw;
-- Result: The table no longer exists in the database.
```

---

## 🏆 Final Capstone Task: "The Data Engineer's Challenge"

**Scenario:** You are building a schema for an **AI-Powered Invoice Bot**. Create the following tables step-by-step:

1.  **`vendors`**: 
    *   `vendor_id` (PK, Auto-increment)
    *   `company_name` (Not Null)
    *   `tax_id` (Unique)
2.  **`invoices`**: 
    *   `invoice_id` (PK)
    *   `vendor_id` (FK linked to vendors)
    *   `total_amount` (Decimal, Check > 0)
    *   `status` (Default 'PENDING', Check IN ('PENDING', 'PAID'))
3.  **Task:** 
    *   Create the tables.
    *   Insert 2 dummy vendors.
    *   Try to insert an invoice with a negative amount (Observe the error).
    *   Try to insert an invoice for a non-existent vendor (Observe the error).
    *   Use `TRUNCATE` to clear the invoices.
    *   Use `DROP` to remove the `vendors` table.

---

## 💡 Instructor's "Why This Matters" Summary

| Concept | Data Science Relevance | Data Engineering Relevance |
|---------|------------------------|----------------------------|
| **Data Types** | Ensures correct statistical calculations. | Optimizes storage and pipeline speed. |
| **Constraints** | Reduces time spent on data cleaning. | Prevents "bad data" from entering the warehouse. |
| **Keys (PK/FK)** | Allows for accurate JOINs in analysis. | Maintains referential integrity across systems. |
| **TRUNCATE/DROP** | Useful for resetting experiment environments. | Essential for managing staging tables in ETL. |