# SQL Notes — CS186 Berkeley

## SQL 1

> Basic selects, filtering, ordering, aggregation.

*(notes to fill in)*

---

## SQL 2

> Subqueries, joins, set operations, advanced aggregation.

*(notes to fill in)*

---

## Advanced Queries — Things Worth Revisiting

### Window Functions

```sql
-- Running total per group
SELECT
    name,
    department,
    salary,
    SUM(salary) OVER (PARTITION BY department ORDER BY salary) AS running_total
FROM employees;
```

**Key clauses:**
- `PARTITION BY` — resets the window per group (like GROUP BY but keeps all rows)
- `ORDER BY` inside OVER — defines the frame ordering
- Common functions: `ROW_NUMBER()`, `RANK()`, `DENSE_RANK()`, `LAG()`, `LEAD()`, `SUM()`, `AVG()`

---

### Subqueries vs JOINs

```sql
-- Correlated subquery: runs once per outer row (can be slow)
SELECT name
FROM students s
WHERE grade > (
    SELECT AVG(grade) FROM students WHERE dept = s.dept
);

-- Usually better rewritten as a JOIN with a derived table
SELECT s.name
FROM students s
JOIN (
    SELECT dept, AVG(grade) AS avg_grade
    FROM students
    GROUP BY dept
) dept_avg ON s.dept = dept_avg.dept
WHERE s.grade > dept_avg.avg_grade;
```

---

### Set Operations

```sql
-- UNION removes duplicates, UNION ALL keeps them
SELECT city FROM customers
UNION
SELECT city FROM suppliers;

-- INTERSECT / EXCEPT (not in MySQL, available in Postgres, SQLite)
SELECT student_id FROM enrolled_2023
INTERSECT
SELECT student_id FROM enrolled_2024;
```

---

### GROUP BY Pitfalls

```sql
-- Every column in SELECT must be in GROUP BY or wrapped in an aggregate
-- This is WRONG in strict SQL:
SELECT dept, name, MAX(salary) FROM employees GROUP BY dept;
--                ^^^^ not in GROUP BY and not aggregated

-- HAVING filters after aggregation (WHERE filters before)
SELECT dept, AVG(salary)
FROM employees
GROUP BY dept
HAVING AVG(salary) > 50000;
```

---

### NULL Behavior

- `NULL = NULL` is `UNKNOWN`, not `TRUE` — use `IS NULL` / `IS NOT NULL`
- Aggregates ignore NULLs (`COUNT(col)` vs `COUNT(*)`)
- `NULL` in a `NOT IN` subquery can make the whole result empty

```sql
-- Dangerous: if any value in the sublist is NULL, returns no rows
SELECT * FROM a WHERE id NOT IN (SELECT id FROM b); -- b.id has NULLs?

-- Safer
SELECT * FROM a WHERE NOT EXISTS (
    SELECT 1 FROM b WHERE b.id = a.id
);
```
