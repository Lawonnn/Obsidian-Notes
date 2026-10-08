# Joins
In relational databases like `PostgreSQL`, a `JOIN` combines rows from two or more tables based on a shared column relationship, typically linking a `Foreign Key` in one table to a 

![[Pasted image 20261002153656.jpg]]

## JOIN Types Cheat Sheet

| **JOIN Type**    | **Logic / Behavior**                                                     | **Resulting Rows**                                     | **Primary Use Case**                                                                              |
| ---------------- | ------------------------------------------------------------------------ | ------------------------------------------------------ | ------------------------------------------------------------------------------------------------- |
| **`INNER JOIN`** | Matches rows present in **both** tables.                                 | Retains only strictly matching records.                | Fetching linked data (e.g., users with assigned SSH keys).                                        |
| **`LEFT JOIN`**  | Keeps **all** left table rows; pads right side with `NULL` if missing.   | All left rows + matching right rows.                   | Auditing missing relations (e.g., finding users _without_ SSH keys using `WHERE key_id IS NULL`). |
| **`RIGHT JOIN`** | Keeps **all** right table rows; pads left side with `NULL` if missing.   | All right rows + matching left rows.                   | Inverse of `LEFT JOIN`; identifying orphaned child records.                                       |
| **`FULL JOIN`**  | Keeps **all** rows from **both** tables; pads missing sides with `NULL`. | Matched pairs + unmatched left + unmatched right.      | Detecting discrepancies across entire multi-system datasets.                                      |
| **`CROSS JOIN`** | Cartesian Product ($M \times N$). Ignores keys/conditions.               | Every row in Table A paired with every row in Table B. | Generating matrices, permission ACL grids, test payload combinations.                             |

## Core SQL Syntax

``` SQL
-- Schema Context:
-- users (user_id PK, username)
-- ssh_keys (key_id PK, user_id FK -> users.user_id, key_label)

-- 1. INNER JOIN (Strict Matches Only)
SELECT 
    u.username, 
    k.key_label
FROM users u                        -- Left table aliased as 'u'
INNER JOIN ssh_keys k               -- Right table aliased as 'k'
    ON u.user_id = k.user_id;       -- Relational mapping (PK = FK)

-- 2. LEFT JOIN (Preserves All Left Rows)
SELECT 
    u.username, 
    k.key_label
FROM users u
LEFT JOIN ssh_keys k 
    ON u.user_id = k.user_id;

-- 3. Audit Pattern: Find Unmatched Left Records (Orphan/Missing Check)
SELECT 
    u.username
FROM users u
LEFT JOIN ssh_keys k 
    ON u.user_id = k.user_id
WHERE k.key_id IS NULL;            -- Isolates users with 0 SSH keys assigned

-- 4. FULL OUTER JOIN (Preserves Everything)
SELECT 
    u.username, 
    k.key_label
FROM users u
FULL JOIN ssh_keys k 
    ON u.user_id = k.user_id;

-- 5. CROSS JOIN (Cartesian Combination - No ON clause)
SELECT 
    u.username, 
    s.hostname
FROM users u
CROSS JOIN servers s;               -- Pairs every user with every server

-- 6. USING Shortcut (Requires identical column name in both tables)
SELECT 
    user_id,                        -- Unified reference handled by PostgreSQL
    u.username, 
    k.key_label
FROM users u
INNER JOIN ssh_keys k 
    USING (user_id);                -- Equivalent to 'ON u.user_id = k.user_id'
```

## Key Rules for Notes

- **`ON` vs `USING`**: Use **`ON u.user_id = k.user_id`** when column names differ or explicit mapping is required; use **`USING (user_id)`** when the join column name is identical in both tables.
    
- **Table Aliases**: Use short aliases (e.g., `FROM users u`, `JOIN ssh_keys k`) to keep queries clean and explicitly disambiguate shared columns (`u.user_id`).
   
- **Performance Note**: **`INNER JOIN`** and **`LEFT JOIN`** queries rely heavily on indexes on **Foreign Key** columns to execute efficiently.