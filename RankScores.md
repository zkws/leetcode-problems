# Problem: Rank Scores

## Table: Scores

| Column Name | Type    |
|-------------|---------|
| id          | int     |
| score       | decimal |

- `id` is the primary key (column with unique values) for this table.
- Each row contains the score of a game. Score is a floating point value with two decimal places.

## Task
Write a solution to find the rank of the scores. The ranking should follow these rules:
1. Scores are ranked from highest to lowest.
2. Ties receive the same rank.
3. After a tie, the next rank is the subsequent integer (no gaps).
4. Return the result ordered by `score` in descending order.

### Example

**Input:**
Scores table:
| id | score |
|----|-------|
| 1  | 3.50  |
| 2  | 3.65  |
| 3  | 4.00  |
| 4  | 3.85  |
| 5  | 4.00  |
| 6  | 3.65  |

**Output:**
| score | rank |
|-------|------|
| 4.00  | 1    |
| 4.00  | 1    |
| 3.85  | 2    |
| 3.65  | 3    |
| 3.65  | 3    |
| 3.50  | 4    |


## Solutions

### Solution 1: MySQL (Using `DENSE_RANK`)
Error Cause: RANK is a reserved keyword in MySQL 8.0+. Using it directly as an alias will cause an error.

Solution: Escape the alias with backticks.
```sql
SELECT 
    score,
    DENSE_RANK() OVER (ORDER BY score DESC) AS `rank`
FROM Scores;
```

### Solution 2: MySQL (Self-Join Approach)
```sql
SELECT 
    s.score,
    COUNT(DISTINCT t.score) AS `rank`
FROM Scores s 
JOIN Scores t ON s.score <= t.score
GROUP BY s.id
ORDER BY s.score DESC;
```

### Solution 3: Pandas
```python
import pandas as pd

def order_scores(scores: pd.DataFrame) -> pd.DataFrame:
    # Assign ranks using 'dense' method (no gaps)
    scores['rank'] = scores['score'].rank(method='dense', ascending=False)
  
    # Drop 'id' column and sort by score
    result_df = scores.drop('id', axis=1).sort_values(by='score', ascending=False)
  
    return result_df
```

---

## Pandas `rank()` Method: `method` Parameter Options

The `method` parameter in `pandas.rank()` determines how to handle ties. Available options:

### 1. `average` (Default)
- **Rule**: Assigns the average rank of the group.
- **Example**:
  ```python
  [3, 2, 2, 4].rank(method='average')  # Output: [3.0, 1.5, 1.5, 4.0]
  ```

### 2. `min`
- **Rule**: Assigns the lowest rank in the group.
- **Example**:
  ```python
  [3, 2, 2, 4].rank(method='min')  # Output: [3.0, 1.0, 1.0, 4.0]
  ```

### 3. `max`
- **Rule**: Assigns the highest rank in the group.
- **Example**:
  ```python
  [3, 2, 2, 4].rank(method='max')  # Output: [3.0, 2.0, 2.0, 4.0]
  ```

### 4. `first`
- **Rule**: Assigns ranks in the order ties appear.
- **Example**:
  ```python
  [3, 2, 2, 4].rank(method='first')  # Output: [3.0, 1.0, 2.0, 4.0]
  ```

### 5. `dense`
- **Rule**: No gaps between ranks after ties.
- **Example**:
  ```python
  [3, 2, 2, 4].rank(method='dense')  # Output: [2.0, 1.0, 1.0, 3.0]
  ```

### Use Case Comparison
| Method    | Scenario                                   |
|-----------|--------------------------------------------|
| `average` | Academic rankings (allow decimal ranks)    |
| `min`     | Competitions (assign lowest possible rank) |
| `max`     | Resource allocation (prioritize highest)   |
| `first`   | First-come-first-served logic              |
| `dense`   | Hierarchical rankings (no gaps)            |

---

### Example Code for `rank()` Methods
```python
import pandas as pd

data = [3, 2, 2, 4]
df = pd.DataFrame({'values': data})

# Calculate ranks with different methods
df['average'] = df['values'].rank(method='average')
df['min'] = df['values'].rank(method='min')
df['max'] = df['values'].rank(method='max')
df['first'] = df['values'].rank(method='first')
df['dense'] = df['values'].rank(method='dense')

print(df)
```

**Output**:
|   values | average | min | max | first | dense |
|---------|---------|-----|-----|-------|-------|
| 0 | 3     | 3.0 | 3.0 | 3.0 | 3.0   | 2.0   |
| 1 | 2     | 1.5 | 1.0 | 2.0 | 1.0   | 1.0   |
| 2 | 2     | 1.5 | 1.0 | 2.0 | 2.0   | 1.0   |
| 3 | 4     | 4.0 | 4.0 | 4.0 | 4.0   | 3.0   |
```
