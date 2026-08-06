# Experiment 3 — Aggregate Functions, GROUP BY, HAVING, DISTINCT & Subqueries

**Course:** Advanced Database Management Systems (ADBMS)
**Student:** Kavya Goyal
**UID:** 24BCS12784

---

# Experiment 3.1 — Conditional Counting Using `COUNT` and `CASE`

## Objective

To understand how the `COUNT` aggregate function can be combined with the `CASE` statement to count only those records that satisfy a given condition.

## Problem Statement

Write an SQL query to determine the number of students in each department who have scored more than **80 marks**. Display the count using the alias **Dept_HighScore_Count**.

**Source:** CodeChef SQL Intermediate — GSQ82

## Table Schema — `student`

| Column     | Description        |
| ---------- | ------------------ |
| St_id      | Student ID         |
| St_name    | Student Name       |
| Marks      | Marks Obtained     |
| Department | Student Department |

## Solution

```sql
SELECT department,
       COUNT(CASE WHEN marks > 80 THEN 1 ELSE NULL END) AS Dept_HighScore_Count
FROM student
GROUP BY department;
```

## Explanation

* The `CASE` statement checks each student's marks.
* If the marks are greater than **80**, it returns **1**.
* Otherwise, it returns **NULL**.
* Since `COUNT()` ignores `NULL` values, only students satisfying the condition are counted.
* `GROUP BY department` groups students according to their respective departments, producing one result for each department.

## Sample Output

| Department | Dept_HighScore_Count |
| ---------- | -------------------- |
| Biology    | 0                    |
| English    | 0                    |

---

# Experiment 3.2 — Aggregate Functions, GROUP BY, HAVING & DISTINCT

## Objective

To practice SQL aggregate functions such as `COUNT`, `SUM`, `MIN`, and `MAX`, along with the use of `GROUP BY`, `ORDER BY`, `HAVING`, and `DISTINCT` clauses on an employee database.

## Table Schema — `employees`

```sql
CREATE TABLE employees (
    emp_id INT PRIMARY KEY,
    emp_name VARCHAR(100) NOT NULL,
    emp_salary DECIMAL(10,2) NOT NULL,
    emp_city VARCHAR(100) NOT NULL
);
```

## Sample Data

| Emp ID | Employee Name | Salary    | City      |
| ------ | ------------- | --------- | --------- |
| 101    | Amit Sharma   | 85000.00  | Mumbai    |
| 102    | Priya Patel   | 95000.00  | Mumbai    |
| 103    | Rahul Verma   | 60000.00  | Delhi     |
| 104    | Ananya Iyer   | 110000.00 | Bangalore |
| 105    | Vikram Singh  | 55000.00  | Delhi     |
| 106    | Sneha Reddy   | 105000.00 | Bangalore |
| 107    | Rohan Das     | 72000.00  | Kolkata   |

---

## Query 1 — Count Employees in Each City

```sql
SELECT emp_city, COUNT(*) AS cnt
FROM employees
GROUP BY emp_city;
```

### Output

| City      | Count |
| --------- | ----- |
| Bangalore | 2     |
| Delhi     | 2     |
| Kolkata   | 1     |
| Mumbai    | 2     |

---

## Query 2 — Employee Count per City (Ascending Order)

### Using `COUNT(*)`

```sql
SELECT emp_city, COUNT(*) AS cnt
FROM employees
GROUP BY emp_city
ORDER BY cnt ASC;
```

### Using `COUNT(emp_id)`

```sql
SELECT emp_city, COUNT(emp_id) AS cnt
FROM employees
GROUP BY emp_city
ORDER BY cnt;
```

Both queries generate identical results because `emp_id` is a non-null primary key.

---

## Query 3 — Employees Earning at Least ₹90,000 (Conditional Aggregation)

### Method 1 — `SUM` with `CASE`

```sql
SELECT emp_city,
       SUM(CASE WHEN emp_salary >= 90000 THEN 1 ELSE 0 END) AS cnt
FROM employees
GROUP BY emp_city
ORDER BY cnt DESC, emp_city DESC;
```

### Method 2 — `COUNT` with `CASE`

```sql
SELECT emp_city,
       COUNT(CASE WHEN emp_salary >= 90000 THEN 1 END) AS cnt
FROM employees
GROUP BY emp_city;
```

### Observation

Both methods produce the same output.

* `SUM(CASE ... THEN 1 ELSE 0 END)` counts by adding **1** for matching rows.
* `COUNT(CASE ... THEN 1 END)` counts only the non-null values returned by the `CASE` expression.

---

## Query 4 — Highest Salary in Each City

```sql
SELECT emp_city, MAX(emp_salary) AS max_salary
FROM employees
GROUP BY emp_city;
```

---

## Query 5 — Lowest Salary in Each City

```sql
SELECT emp_city, MIN(emp_salary) AS min_salary
FROM employees
GROUP BY emp_city;
```

---

## Query 6 — Cities with Minimum Salary of at Least ₹85,000

```sql
SELECT emp_city, MIN(emp_salary) AS min_salary
FROM employees
GROUP BY emp_city
HAVING MIN(emp_salary) >= 85000;
```

### Difference Between `WHERE` and `HAVING`

* `WHERE` filters individual rows before grouping.
* `HAVING` filters grouped results after aggregate calculations have been performed.

---

## Query 7 — Display Unique Cities

```sql
SELECT DISTINCT emp_city
FROM employees;
```

`DISTINCT` removes duplicate values and returns only unique city names.

---

# Experiment 3.3 — Customers Who Never Placed an Order (`NOT IN` Subquery)

## Objective

To understand how subqueries combined with the `NOT IN` operator can be used to retrieve records that do not have corresponding entries in another table.

## Problem Statement

Write an SQL query to list the names of customers who have never placed an order.

**Problem:** LeetCode 183 — Customers Who Never Order

**Difficulty:** Easy

---

## Table Schema

### Customers

| Column | Type    |
| ------ | ------- |
| id     | INT     |
| name   | VARCHAR |

* `id` is the primary key.

### Orders

| Column     | Type |
| ---------- | ---- |
| id         | INT  |
| customerId | INT  |

* `id` is the primary key.
* `customerId` is a foreign key referencing `Customers.id`.

---

## Solution

```sql
SELECT name AS Customers
FROM Customers
WHERE id NOT IN (
    SELECT customerId
    FROM Orders
);
```

---

## Explanation

* The inner query retrieves the IDs of customers who have placed at least one order.
* The outer query checks every customer ID against this list.
* Customers whose IDs are **not present** in the subquery result are returned.
* These are the customers who have never placed any order.

---

## Result

**Status:** ✅ Accepted (Runtime: 113 ms)

---

# Key Concepts Covered

| Concept    | Description                                                              |
| ---------- | ------------------------------------------------------------------------ |
| `GROUP BY` | Groups rows having the same value so aggregate functions can be applied. |
| `HAVING`   | Filters grouped data based on aggregate conditions.                      |
| `ORDER BY` | Sorts query results in ascending or descending order.                    |
| `DISTINCT` | Eliminates duplicate values from the output.                             |
| `COUNT()`  | Counts non-null values or all rows when using `*`.                       |
| `SUM()`    | Calculates the total of a numeric column.                                |
| `MIN()`    | Returns the smallest value in a column.                                  |
| `MAX()`    | Returns the largest value in a column.                                   |
| `CASE`     | Implements conditional logic within SQL queries.                         |
| `NOT IN`   | Filters rows whose values are absent from a subquery result.             |
| `Subquery` | A query nested inside another SQL statement.                             |

---

# Tools Used

* **CodeChef SQL Intermediate** — Experiment 3.1
* **Programiz Online SQL Compiler** — Experiment 3.2
* **LeetCode** — Experiment 3.3
