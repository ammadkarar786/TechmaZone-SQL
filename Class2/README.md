
## 1. **Primary Key**

A primary key uniquely identifies each record and cannot be NULL.

**Example: Employees Table**
```sql
CREATE TABLE Employees (
    EmployeeID INT PRIMARY KEY,
    FirstName VARCHAR(50),
    LastName VARCHAR(50),
    Email VARCHAR(100)
);
```

| EmployeeID | FirstName | LastName | Email |
|------------|-----------|----------|-------|
| 101 | Ammad | Karar | ammad@example.com |
| 102 | Sarah | Ahmed | sarah@example.com |
| 103 | Ali | Hassan | ali@example.com |

- **EmployeeID** is the primary key
- Each value is unique
- No NULL values allowed
- Only ONE primary key per table

---

## 2. **Unique Key**

Ensures uniqueness but allows ONE NULL value.

**Example: Employees with Unique Email**
```sql
CREATE TABLE Employees (
    EmployeeID INT PRIMARY KEY,
    FirstName VARCHAR(50),
    Email VARCHAR(100) UNIQUE,
    Phone VARCHAR(20)
);
```

| EmployeeID | FirstName | Email | Phone |
|------------|-----------|-------|-------|
| 101 | Ammad | ammad@example.com | +92-334-1234567 |
| 102 | Sarah | sarah@example.com | +92-334-2345678 |
| 103 | Ali | NULL | +92-334-3456789 |
| 104 | Fatima | fatima@example.com | NULL |

- **Email** has a UNIQUE constraint
- Can have one NULL (Ali's email)
- All non-NULL values must be unique
- You can have multiple unique keys in a table

---

## 3. **Foreign Key**

Creates a relationship between two tables by referencing another table's primary key.

**Example: Departments and Employees**

**Departments Table:**
```sql
CREATE TABLE Departments (
    DeptID INT PRIMARY KEY,
    DeptName VARCHAR(50)
);
```

| DeptID | DeptName |
|--------|----------|
| 1 | IT |
| 2 | HR |
| 3 | Finance |

**Employees Table with Foreign Key:**
```sql
CREATE TABLE Employees (
    EmployeeID INT PRIMARY KEY,
    FirstName VARCHAR(50),
    DeptID INT,
    FOREIGN KEY (DeptID) REFERENCES Departments(DeptID)
);
```

| EmployeeID | FirstName | DeptID |
|------------|-----------|--------|
| 101 | Ammad | 1 |
| 102 | Sarah | 2 |
| 103 | Ali | 1 |

- **DeptID** in Employees is a foreign key
- It references **DeptID** (primary key) in Departments
- Ensures referential integrity (can't assign employee to non-existent department)

---

## 4. **Candidate Keys**

All columns (or combinations) that could potentially be primary keys.

**Example: Students Table**
```sql
CREATE TABLE Students (
    StudentID INT,
    Email VARCHAR(100),
    NationalID VARCHAR(20),
    FirstName VARCHAR(50),
    LastName VARCHAR(50)
);
```

| StudentID | Email | NationalID | FirstName | LastName |
|-----------|-------|------------|-----------|----------|
| 1001 | ammad@uni.edu | 42101-1234567-8 | Ammad | Karar |
| 1002 | sarah@uni.edu | 42101-2345678-9 | Sarah | Ahmed |
| 1003 | ali@uni.edu | 42101-3456789-0 | Ali | Hassan |

**Candidate Keys:**
- **StudentID** - unique for each student
- **Email** - unique for each student
- **NationalID** - unique for each citizen

You choose ONE as the primary key (typically StudentID). The others remain candidate keys and can have UNIQUE constraints.

---

## 5. **Composite Key**

A primary key made from multiple columns when no single column is unique.

**Example: Course Enrollment**
```sql
CREATE TABLE Enrollments (
    StudentID INT,
    CourseCode VARCHAR(10),
    EnrollmentDate DATE,
    Grade CHAR(2),
    PRIMARY KEY (StudentID, CourseCode)
);
```

| StudentID | CourseCode | EnrollmentDate | Grade |
|-----------|------------|----------------|-------|
| 1001 | CS101 | 2025-01-15 | A |
| 1001 | CS102 | 2025-01-15 | B+ |
| 1002 | CS101 | 2025-01-15 | A- |
| 1002 | MATH201 | 2025-01-15 | B |

- Neither **StudentID** nor **CourseCode** alone is unique
- **Combination (StudentID + CourseCode)** is unique
- This composite key prevents duplicate enrollments

---

## 6. **Natural Key**

A key with real-world business meaning.

**Example: Products with Natural Key**
```sql
CREATE TABLE Products (
    ProductSKU VARCHAR(20) PRIMARY KEY,
    ProductName VARCHAR(100),
    Price DECIMAL(10,2)
);
```

| ProductSKU | ProductName | Price |
|------------|-------------|-------|
| LAPTOP-HP-15-001 | HP Laptop 15" | 85000 |
| PHONE-SAM-S23-002 | Samsung S23 | 120000 |
| TAB-APPLE-IPAD-003 | Apple iPad | 95000 |

- **ProductSKU** is a natural key
- Has business meaning (brand, type, model)
- Derived from real-world attributes
- Can be long and complex

---

## 7. **Surrogate Key**

An artificial, system-generated key with no business meaning.

**Example: Products with Surrogate Key**
```sql
CREATE TABLE Products (
    ProductID INT AUTO_INCREMENT PRIMARY KEY,
    ProductSKU VARCHAR(20) UNIQUE,
    ProductName VARCHAR(100),
    Price DECIMAL(10,2)
);
```

| ProductID | ProductSKU | ProductName | Price |
|-----------|------------|-------------|-------|
| 1 | LAPTOP-HP-15-001 | HP Laptop 15" | 85000 |
| 2 | PHONE-SAM-S23-002 | Samsung S23 | 120000 |
| 3 | TAB-APPLE-IPAD-003 | Apple iPad | 95000 |

- **ProductID** is a surrogate key
- Auto-incrementing number (1, 2, 3...)
- No business meaning
- Simple and efficient for joins
- **ProductSKU** becomes a unique key instead



# -------------------------------------------------------------------------------------------------------------------------------------------------
Here is a complete explanation of the `WHERE` clause variations, including a custom dataset and SQL examples for every operation mentioned in your tutorial.

### 1. The Dataset
First, let's create a table called `Customer_Feedback_Constraints1` (based on your image) and populate it with diverse data to demonstrate all the filtering operations.

```sql
-- Create the table based on your screenshot
CREATE TABLE Customer_Feedback_Constraints1 (
    id INT PRIMARY KEY AUTO_INCREMENT,
    customer_name VARCHAR(50) NOT NULL,
    rating INT NOT NULL CHECK (rating >= 1 AND rating <= 5),
    comments VARCHAR(255),
    Submission_date TIMESTAMP NOT NULL DEFAULT (CURRENT_DATE)
);

-- Insert a diverse dataset for testing
INSERT INTO Customer_Feedback_Constraints1 
(customer_name, rating, comments, Submission_date) VALUES
('Tiffany', 5, 'Excellent service!', '2022-03-15'),
('Tommy', 4, 'Good product quality.', '2023-01-10'),
('Sarah', 3, 'Average experience.', '2021-11-20'),
('Tanya Foy', 5, 'Best purchase ever!', '2022-08-05'),
('Mike', 2, 'Delivery was late.', '2023-06-12'),
('France User', 4, 'Great support team.', '2022-05-18'),
('Toby', 1, 'Terrible packaging.', '2021-02-28'),
('Alice', 5, 'Will buy again.', '2023-09-01');
```

---

### 2. Basic Filtering (Single Condition)
**Concept:** Retrieve rows that match one specific criteria exactly.

**Example:** Find all customers who gave a perfect 5-star rating.
```sql
SELECT * FROM Customer_Feedback_Constraints1
WHERE rating = 5;
```
*Result:* Returns Tiffany, Tanya Foy, and Alice.

---

### 3. Multiple Conditions with `AND`
**Concept:** All conditions must be true. As per your tutorial, **always use parentheses** when combining conditions to prevent logic errors.

**Example:** Find customers named 'Tiffany' who also gave a 5-star rating.
```sql
SELECT * FROM Customer_Feedback_Constraints1
WHERE (customer_name = 'Tiffany' AND rating = 5);
```
*Result:* Returns only Tiffany. (Tommy is excluded because his rating is 4).

---

### 4. Filtering with Mathematical Operators
**Concept:** Use `>`, `<`, `>=`, `<=` for numeric or date comparisons.

**Example:** Find all feedback submitted after January 1st, 2023, with a rating greater than 3.
```sql
SELECT * FROM Customer_Feedback_Constraints1
WHERE (Submission_date > '2023-01-01' AND rating > 3);
```
*Result:* Returns Mike (rating 2 is excluded), Alice (rating 5 included).

---

### 5. Logical Choices with `OR` (and mixing with `AND`)
**Concept:** At least one condition must be true. **Crucial:** When mixing `AND` and `OR`, you MUST group the `OR` conditions in parentheses, otherwise SQL processes `AND` first, leading to silent bugs.

**Example:** Find high-rated feedback (rating >= 4) from either 'Tiffany' OR 'Tanya Foy'.
```sql
-- CORWAY: Grouping OR conditions
SELECT * FROM Customer_Feedback_Constraints1
WHERE (rating >= 4) AND (customer_name = 'Tiffany' OR customer_name = 'Tanya Foy');

-- WRONG WAY (Avoid this):
-- WHERE rating >= 4 AND customer_name = 'Tiffany' OR customer_name = 'Tanya Foy'
-- This would return Tanya Foy even if her rating was 1!
```
*Result:* Returns Tiffany and Tanya Foy.

---

### 6. Pattern Matching with `LIKE`
**Concept:** Filter strings using wildcards. `%` = any sequence of characters, `_` = exactly one character.

| Pattern | Meaning | Example Query |
| :--- | :--- | :--- |
| `'T%'` | Starts with T | `WHERE customer_name LIKE 'T%'` |
| `'T%Y'` | Starts with T, ends with Y | `WHERE customer_name LIKE 'T%Y'` |
| `'T__F%Y'` | Starts with T, 2 chars, then F, then anything, ends with Y | `WHERE customer_name LIKE 'T__F%Y'` |

**Examples using our dataset:**
```sql
-- Names starting with 'T'
SELECT * FROM Customer_Feedback_Constraints1 WHERE customer_name LIKE 'T%';
-- Returns: Tiffany, Tommy, Tanya Foy, Toby

-- Names starting with 'T' and ending with 'y'
SELECT * FROM Customer_Feedback_Constraints1 WHERE customer_name LIKE 'T%y';
-- Returns: Tiffany, Tommy, Tanya Foy, Toby (case-insensitive in most SQL)

-- Complex pattern: T, then 2 chars, then 'n', then anything
SELECT * FROM Customer_Feedback_Constraints1 WHERE customer_name LIKE 'T__n%';
-- Returns: Tiffany (T-i-f-f-a-n-y... wait, let's check: T(1)i(2)f(3)f(4)a(5)n(6)y)
-- Actually 'T__n%' means T + 2chars + n + anything. 
-- 'Tiffany' = T + i + f + f + a + n + y. The 'n' is at position 6.
-- Let's use a better example for T__F%Y from your tutorial:
SELECT * FROM Customer_Feedback_Constraints1 WHERE customer_name LIKE 'T__F%Y';
-- Returns: Tanya Foy (T-a-n-y-a- -F-o-y... hmm, space counts as char)
-- Actually 'Tanya Foy': T(1)a(2)n(3)y(4)a(5) (6)F(7)o(8)y(9)
-- T__F%Y = T + 2chars + F + % + Y. Position 3 should be F. 
-- In 'Tanya Foy', position 3 is 'n'. So this won't match.
-- Let me adjust the example to match your tutorial exactly:
-- If we had a name like 'TofFey', it would match T__F%Y
```

> **Note:** For the exact pattern `T__F%Y` from your tutorial, you'd need a name like "TofFey" or "TabFay". With our current dataset, `LIKE 'T%F%'` would match "Tanya Foy".

---

### 7. Filtering a List of Values with `IN`
**Concept:** Cleaner alternative to multiple `OR` statements.

**Example:** Find feedback from specific customers without writing multiple ORs.
```sql
SELECT * FROM Customer_Feedback_Constraints1
WHERE customer_name IN ('Tiffany', 'Sarah', 'Alice');
```
*Result:* Returns Tiffany, Sarah, and Alice. Much cleaner than `WHERE name='Tiffany' OR name='Sarah' OR name='Alice'`.

---

### 8. Targeting Rows in DML Commands (UPDATE & DELETE)
**Concept:** The `WHERE` clause is critical in Data Manipulation Language. Without it, you affect ALL rows.

#### UPDATE with WHERE
**Example:** Update only Tiffany's comment (not everyone's!).
```sql
UPDATE Customer_Feedback_Constraints1
SET comments = 'Updated: Truly exceptional!'
WHERE id = 1;  -- Only affects Tiffany's row
```

#### DELETE with WHERE
**Example:** Remove only low-rated feedback (rating = 1), not the entire table!
```sql
DELETE FROM Customer_Feedback_Constraints1
WHERE rating = 1;  -- Only deletes Toby's row
```

> ⚠️ **WARNING:** Running `DELETE FROM Customer_Feedback_Constraints1;` without a WHERE clause will delete ALL records permanently! Always test with `SELECT` first using the same WHERE condition.

---

### Quick Reference Summary

| Operation | Operator/Keyword | Best Practice |
|-----------|-----------------|---------------|
| Exact Match | `=` | Use for IDs, exact strings |
| Combine Filters | `AND` | Always use parentheses |
| Numeric/Date Range | `>`, `<`, `>=` | Great for dates and scores |
| Alternative Filters | `OR` | Group OR conditions in () when mixed with AND |
| String Patterns | `LIKE '%_'` | `%` = many chars, `_` = one char |
| Value Lists | `IN (...)` | Replaces multiple OR statements |
| Safe Updates | `UPDATE ... WHERE` | Never omit WHERE in UPDATE |
| Safe Deletes | `DELETE ... WHERE` | Always SELECT first to verify |




---
