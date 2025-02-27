
# SQL Problem: Find Duplicate Emails

## Problem Description
Given the `Person` table, write a solution to report all duplicate emails. A duplicate email is defined as an email that appears more than once.

### Table Schema
**Table:** `Person`

| Column Name | Type    | Description                               |
|-------------|---------|-------------------------------------------|
| id          | int     | Primary key (unique values).              |
| email       | varchar | Contains emails (no uppercase letters).   |

### Example

**Input:**
`Person` table:
| id | email    |
|----|----------|
| 1  | a@b.com  |
| 2  | c@d.com  |
| 3  | a@b.com  |

**Output:**
| Email     |
|-----------|
| a@b.com   |

**Explanation:** `a@b.com` appears twice.

---

## Solutions

### Solution 1: Using `GROUP BY` and `HAVING`
```sql
SELECT email AS Email
FROM Person
GROUP BY email
HAVING COUNT(email) > 1;
```

### Solution 2: Using `OVER (PARTITION BY)` Window Function
```sql
SELECT DISTINCT email AS Email
FROM (
    SELECT email, COUNT(*) OVER (PARTITION BY email) AS cnt
    FROM Person
) AS temp
WHERE cnt >= 2;
```

---

## Key Differences: `GROUP BY` vs. `OVER (PARTITION BY)`

### Functional Differences
| Feature                | `GROUP BY`                                                                 | `OVER (PARTITION BY)`                                                                 |
|------------------------|----------------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| **Output Rows**        | Returns one row per group (aggregated result).                             | Retains all original rows, adding aggregated results as a new column.                |
| **Granularity**        | Aggregates data at the group level.                                        | Computes aggregated values while preserving individual row details.                  |
| **Usage Scenario**     | Suitable for summary statistics (e.g., totals, averages).                  | Useful for row-level computations (e.g., rankings, cumulative sums).                 |
| **Execution Order**    | Executes after `WHERE` and before `HAVING`.                                | Executes after `FROM` and before `WHERE`/`GROUP BY`.                                  |

### Performance Considerations
1. **`GROUP BY`**
   - Optimized for reducing data volume via aggregation.
   - Efficient if grouping columns are indexed or use hash/sort-based aggregation.
   - Performance degrades with high-cardinality grouping columns (many unique values).

2. **`OVER (PARTITION BY)`**
   - Preserves original rows, which may increase memory usage for large datasets.
   - Avoids additional joins to retain original data.
   - Performance depends on partition key alignment with data order and database optimizations (e.g., streaming in PostgreSQL).

---

## Execution Order in SQL Queries
The standard execution order is:
1. **FROM**
2. **WHERE** (filters data before grouping)
3. **GROUP BY** (groups data)
4. **Aggregate Functions** (e.g., `COUNT`, `SUM`)
5. **HAVING** (filters grouped data)
6. **ORDER BY** (sorts results)
7. **SELECT** (final projection)

### `HAVING` vs. `WHERE`
| Criteria               | `WHERE`                                  | `HAVING`                                |
|------------------------|------------------------------------------|-----------------------------------------|
| **Execution Timing**   | Filters data **before** grouping.        | Filters data **after** grouping.        |
| **Aggregate Functions**| Cannot use aggregate functions.          | Can use aggregate functions (e.g., `COUNT`). |
| **Column Scope**       | Operates on individual columns.          | Operates on grouped columns/aggregates. |

---

## Best Practices for Performance
1. **Filter Early**: Use `WHERE` to reduce dataset size before grouping or window operations.
2. **Indexing**: Add indexes to columns used in `GROUP BY` or `PARTITION BY` clauses.
3. **Avoid Redundant Columns**: Minimize columns in `SELECT` to reduce memory usage.
4. **Test Execution Plans**: Analyze query execution plans to identify bottlenecks. 
