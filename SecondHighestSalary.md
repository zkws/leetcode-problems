# Employee Table Schema

| Column Name | Type   |
|-------------|--------|
| id          | int    |
| salary      | int    |

- `id` is the primary key (column with unique values)
- Each row contains salary information of an employee

# Problem Statement
Write a SQL query to find the second highest **distinct** salary from the Employee table. If there is no second highest salary, return `null` (return `None` in Pandas).

# Examples

## Example 1
**Input:**  
| id | salary |
|----|--------|
| 1  | 100    |
| 2  | 200    |
| 3  | 300    |

**Output:**  
| SecondHighestSalary |
|---------------------|
| 200                 |

## Example 2
**Input:**  
| id | salary |
|----|--------|
| 1  | 100    |

**Output:**  
| SecondHighestSalary |
|---------------------|
| null                |

# Solutions

## Solution 1: Using ROW_NUMBER()
```sql
SELECT
CASE
    WHEN count(*) = 0 THEN null
    ELSE salary
END AS SecondHighestSalary
FROM(
SELECT *,
ROW_NUMBER() OVER (ORDER BY salary DESC) AS r
FROM (SELECT DISTINCT salary FROM Employee) AS A) AS B
WHERE r = 2;
```

## Solution 2: Using Subquery with MAX
```sql
SELECT MAX(salary) AS SecondHighestSalary 
FROM Employee
WHERE salary < (SELECT salary FROM Employee ORDER BY salary DESC LIMIT 1);
```

## Solution 3: Using LIMIT-OFFSET
```sql
SELECT
(SELECT DISTINCT Salary 
FROM Employee 
ORDER BY salary DESC 
LIMIT 1 OFFSET 1) 
AS SecondHighestSalary;
```

# LIMIT and OFFSET Usage

## Syntax Comparison
1. `SELECT * FROM article LIMIT 1,3`
2. `SELECT * FROM article LIMIT 3 OFFSET 1`

Both statements return records 2, 3, 4 (skip 1, take 3)

## Key Points
- **Two-parameter LIMIT**: `LIMIT x,y` = Skip x rows, return y rows
- **Single-parameter LIMIT**: `LIMIT n` = Return first n rows (equivalent to `LIMIT 0,n`)
- **LIMIT-OFFSET syntax**: Clearer syntax for pagination

## Important Notes
1. Always use `ORDER BY` with `LIMIT/OFFSET` for predictable results
2. `DISTINCT` is crucial when dealing with potential duplicate records
3. Handle null cases explicitly using `CASE` or subquery wrappers
