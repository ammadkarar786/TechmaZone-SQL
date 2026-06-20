
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

---
