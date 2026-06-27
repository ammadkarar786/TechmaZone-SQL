

##  Learning Objectives
By the end of this lesson, students will be able to:
-   Understand the difference between row-level filtering (`WHERE`) and group-level filtering (`HAVING`).
-   Use aggregate functions (`SUM`, `COUNT`, `AVG`, `MIN`, `MAX`) effectively.
-   Organize data into logical buckets using `GROUP BY`.
-   Rename columns for readability using Aliases (`AS`).
-   Sort aggregated results using `ORDER BY`.

## 🗂️ Dataset Overview
The project uses a generated SQLite database (`techgear_pro.db`) representing an electronics retail chain. All exercises focus on the **`Sales`** table, which contains:

| Column | Type | Description |
| :--- | :--- | :--- |
| `sale_id` | INTEGER | Unique transaction ID (Primary Key) |
| `store_id` | INTEGER | Branch identifier |
| `employee_id` | INTEGER | Salesperson identifier |
| `product_id` | INTEGER | Item sold identifier |
| `sale_date` | DATETIME | Timestamp of transaction |
| `quantity` | INTEGER | Number of units sold |
| `total_amount` | REAL | Final price of the transaction |

##  Key Concepts Cheat Sheet

### 1. Aggregate Functions
Functions that perform a calculation on a set of values and return a single value.
```sql
SUM(column)      -- Total sum
COUNT(column)    -- Number of non-null rows
AVG(column)      -- Average value
MIN(column)      -- Minimum value
MAX(column)      -- Maximum value
```

### 2. GROUP BY
Collapses rows into summary rows based on one or more columns.
> ️ **Rule:** Every column in your `SELECT` clause must either be in the `GROUP BY` clause OR be inside an aggregate function.

### 3. Aliases (AS)
Renames columns or tables temporarily for better readability.
```sql
SELECT SUM(total_amount) AS total_revenue FROM Sales;
```

### 4. WHERE vs. HAVING
This is the most critical distinction in aggregation:

| Feature | WHERE | HAVING |
| :--- | :--- | :--- |
| **Filters** | Individual rows *before* grouping | Groups *after* aggregation |
| **Works with Aggregates?** |  No | ✅ Yes |
| **Performance** | Faster (reduces rows early) | Slower (processes all groups first) |

### 5. ORDER BY
Sorts the final result set. Can use column names, aliases, or position numbers.
```sql



ORDER BY total_revenue DESC  -- Highest revenue first
```

```sql
-- Q1: Calculate total revenue per store. Display Store ID and label the sum as 'total_revenue'.

-- Q2: Identify stores with total revenue exceeding $50,000. Show only these high-performing stores.

-- Q3: For each employee, calculate number of sales, total revenue, and average sale value. 
--     Label columns clearly and sort results by highest revenue first.

-- Q4: Find employees who completed more than 20 sales in the last 6 months. 
--     Display their ID, recent sale count, and recent revenue.

-- Q5: List product IDs where total quantity sold exceeds 15 units. Sort by quantity descending.

-- Q6: Identify employees whose average sale value is above $800 (premium item sellers). 
--     Show employee ID and their average sale value.

-- Q7: Find underperforming stores with total revenue below $30,000 AND fewer than 100 transactions. 
--     Show store ID, revenue, and transaction count.

-- Q8: Group sales by date and show daily total revenue, but only for days where revenue exceeded $2,000. 
--     Sort chronologically.

-- Q9: Look at bulk purchases (quantity > 2). Among these, find employees whose total bulk-sale revenue 
--     exceeds $5,000. Include bulk sale count and total bulk revenue, sorted by revenue descending.
```
