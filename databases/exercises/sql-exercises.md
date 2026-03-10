# SQL Exercises

A mix of self-written and sourced problems. Solve before looking at the solution.

---

## Exercise 1 — Second Highest Salary

**Problem:** Find the second highest salary from an `employees` table. Return NULL if it doesn't exist.

```sql
-- Schema
CREATE TABLE employees (id INT, name TEXT, salary INT);
```

<details>
<summary>Solution</summary>

```sql
SELECT MAX(salary) AS second_highest
FROM employees
WHERE salary < (SELECT MAX(salary) FROM employees);
```

Or with window functions:
```sql
SELECT DISTINCT salary
FROM (
    SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
    FROM employees
) ranked
WHERE rnk = 2;
```

</details>

---

## Exercise 2 — Departments with No Employees

**Problem:** Find departments that have no employees.

```sql
-- Schema
CREATE TABLE departments (id INT, name TEXT);
CREATE TABLE employees (id INT, name TEXT, dept_id INT);
```

<details>
<summary>Solution</summary>

```sql
-- Using LEFT JOIN
SELECT d.name
FROM departments d
LEFT JOIN employees e ON d.id = e.dept_id
WHERE e.id IS NULL;

-- Using NOT EXISTS
SELECT name FROM departments d
WHERE NOT EXISTS (
    SELECT 1 FROM employees WHERE dept_id = d.id
);
```

</details>

---

## Exercise 3 — Students Above Their Department Average

**Problem:** Find students whose grade is above the average grade of their department.

```sql
-- Schema
CREATE TABLE students (id INT, name TEXT, dept TEXT, grade FLOAT);
```

<details>
<summary>Solution</summary>

```sql
SELECT s.name, s.dept, s.grade
FROM students s
JOIN (
    SELECT dept, AVG(grade) AS avg_grade
    FROM students
    GROUP BY dept
) avg ON s.dept = avg.dept
WHERE s.grade > avg.avg_grade;
```

</details>

---

*(Add more exercises as you go)*
