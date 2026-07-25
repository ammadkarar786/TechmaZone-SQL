

---

## Step 1: Set the Stage — Python vs. SQLite Responsibilities 

### 1. The Core Concept


* **Python (Application Layer):** Handles user interfaces, API requests, business logic, and calling the database.
* **SQLite Triggers (Database Layer):** Acts as the **ultimate safety net**. Even if a bug exists in Python, or another developer connects directly to the database, the trigger executes **automatically**.

---

---

### 2. Key Differences to Note

> **Teacher's Note:** Tell students that SQLite handles triggers slightly differently from big server engines (like SQL Server or PostgreSQL):
> 1. **No Stored Procedures in SQLite:** Python functions step in as the procedure layer.
> 2. **`NEW` vs. `OLD` Vectors:** SQLite uses `NEW.col_name` and `OLD.col_name` to read data changes instead of `inserted`/`deleted` tables.
> 3. **String Concatenation:** SQLite uses double pipes (`||`) to combine text and numbers.
> 
> 

---

## Step 2: Set Up the Database & Audit Schema 

### 1. Code to Show Students

Open your Python editor/notebook and write the initial connection and table setup:

```python
import sqlite3

# Connect to an in-memory database for quick testing
conn = sqlite3.connect(":memory:")
cursor = conn.cursor()

# Step 1: Enable Foreign Key support (disabled by default in SQLite)
cursor.execute("PRAGMA foreign_keys = ON;")

# Step 2: Create our Main Table and Audit Table
cursor.executescript("""
CREATE TABLE employees (
    emp_id INTEGER PRIMARY KEY AUTOINCREMENT,
    emp_name TEXT NOT NULL,
    position TEXT NOT NULL,
    salary REAL NOT NULL
);

CREATE TABLE audit_logs (
    log_id INTEGER PRIMARY KEY AUTOINCREMENT,
    event_type TEXT NOT NULL,
    log_message TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
""")
conn.commit()
print("Tables created successfully!")

```

### 2. What to Highlight to Students

* `INTEGER PRIMARY KEY AUTOINCREMENT` creates unique IDs automatically.
* `DEFAULT CURRENT_TIMESTAMP` automatically timestamps log entries without requiring Python to pass a date string.

---

## Step 3: Build & Test an `AFTER INSERT` Audit Trigger 

### 1. Code to Show Students

Now, write the trigger that listens for new hires and automatically writes an entry into `audit_logs`:

```python
# Step 3: Create AFTER INSERT Trigger
cursor.execute("""
CREATE TRIGGER trg_AfterEmployeeInsert
AFTER INSERT ON employees
FOR EACH ROW
BEGIN
    INSERT INTO audit_logs (event_type, log_message)
    VALUES (
        'INSERT',
        'New employee hired: ' || NEW.emp_name || 
        ' (ID: ' || NEW.emp_id || ', Salary: $' || NEW.salary || ')'
    );
END;
""")
conn.commit()

```

### 2. Key Concepts to Explain

* **`AFTER INSERT ON employees`**: Tells SQLite *when* to run this block.
* **`FOR EACH ROW`**: Ensures the trigger runs for every individual record inserted.
* **`NEW.emp_name` / `NEW.emp_id**`: Point out `NEW`. Explain that `NEW` represents the row *being added*.

### 3. Test It Live

Run a test `INSERT` command via Python:

```python
# Insert a new record
cursor.execute("""
    INSERT INTO employees (emp_name, position, salary) 
    VALUES ('Sarah Connor', 'Data Engineer', 95000.00);
""")
conn.commit()

# Check if the trigger automatically wrote a log!
cursor.execute("SELECT * FROM audit_logs;")
print(cursor.fetchall())

```

---

## Step 4: Master `OLD` vs. `NEW` with an `AFTER UPDATE` Trigger 

### 1. Conceptual Breakdown

Draw this simple reference table on the board to explain state vectors:

| Event | `OLD.column` | `NEW.column` |
| --- | --- | --- |
| **`INSERT`** | `NULL` | Incoming New Value |
| **`UPDATE`** | **Value Before Change** | **Value After Change** |
| **`DELETE`** | Value Being Deleted | `NULL` |

### 2. Code to Show Students

Write an update trigger that logs salary revisions:

```python
# Step 4: Create AFTER UPDATE Trigger
cursor.execute("""
CREATE TRIGGER trg_AfterSalaryUpdate
AFTER UPDATE OF salary ON employees
FOR EACH ROW
BEGIN
    INSERT INTO audit_logs (event_type, log_message)
    VALUES (
        'UPDATE',
        'Salary update for ' || NEW.emp_name || ' (ID: ' || NEW.emp_id || 
        '): Old = $' || OLD.salary || ' -> New = $' || NEW.salary
    );
END;
""")
conn.commit()

```

### 3. Test It Live

```python
# Update Sarah's salary
cursor.execute("UPDATE employees SET salary = 110000.00 WHERE emp_id = 1;")
conn.commit()

# Query log
cursor.execute("SELECT log_message FROM audit_logs WHERE event_type = 'UPDATE';")
print(cursor.fetchone()[0])
# Output: Salary update for Sarah Connor (ID: 1): Old = $95000.0 -> New = $110000.0

```

---

## Step 5: Student Guided Practice & Error Handling 

Prompt students to write a **Guard Rail Trigger** that blocks improper data updates before they happen.

> **Challenge:** Write a `BEFORE UPDATE` trigger called `trg_CheckSalaryRaise`.
> If an updated salary is **more than 50% higher** than the old salary (`NEW.salary > OLD.salary * 1.5`), use `RAISE(ABORT, 'error message')` to block the operation.

```

### 3. Show How Python Handles the Error

```python
try:
    # Attempting a 100% raise ($110,000 -> $220,000)
    cursor.execute("UPDATE employees SET salary = 220000.00 WHERE emp_id = 1;")
    conn.commit()
except sqlite3.IntegrityError as e:
    print(f"\n[TRIGGER BLOCKED UPDATE]: {e}")

```

---

## Step 6: Recap & Key Takeaways

Conclude the class by reviewing these three golden rules of Data Engineering:

1. **Automation:** Triggers enforce audit logging and security rules directly in the database, reducing duplicate application logic.
2. **Timing Matters:** Use `BEFORE` triggers to validate/cancel bad data; use `AFTER` triggers to log completed changes.
3. **Keep Triggers Fast:** Never put long, heavy loops inside triggers. Slow triggers slow down every write operation across your database connection.
