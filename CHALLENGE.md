# Advocate4Me Challenge

## English

### Description
UnitedHealth Group (UHG) has a program called Advocate4Me that allows members to call an advocate for various health care needs. Calls into this call center are classified into categories such as:
- Claims and benefits
- Drug coverage
- Authorization
- Medical records
- Emergency assistance
- Member portal services

However, some calls do not fit any of these categories and are labeled as `n/a` or left empty (NULL). Your task is to write a SQL query to calculate the percentage of calls that cannot be categorized (those with `call_category = 'n/a'` or `call_category` IS NULL).

#### Schema
Table: `callers`

| Column Name        | Type      |
|--------------------|-----------|
| policy_holder_id   | integer   |
| case_id            | varchar   |
| call_category      | varchar   |
| call_date          | timestamp |
| call_duration_secs | integer   |

#### Formula

Percentage of uncategorized calls = (Number of uncategorized calls / Total calls) * 100


#### Requirements
1. Count how many calls have `call_category` as `n/a` or NULL.
2. Count the total number of calls in the table.
3. Calculate the percentage by applying the formula above.
4. Round the result to one decimal place.

### Example
Given the following rows:

| policy_holder_id | case_id                              | call_category         | call_date                 | call_duration_secs |
|------------------|--------------------------------------|-----------------------|---------------------------|--------------------|
| 1                | f1d012f9-9d02-4966-a968-bf6c5bc9a9fe | emergency assistance | 2023-04-13T19:16:53Z      | 144                |
| 1                | 41ce8fb6-1ddd-4f50-ac31-07bfcce6aaab | authorisation         | 2023-05-25T09:09:30Z      | 815                |
| 2                | 9b1af84b-eedb-4c21-9730-6f099cc2cc5e | n/a                   | 2023-01-26T01:21:27Z      | 992                |
| 2                | 8471a3d4-6fc7-4bb2-9fc7-4583e3638a9e | emergency assistance | 2023-03-09T10:58:54Z      | 128                |
| 2                | 38208fae-bad0-49bf-99aa-7842ba2e37bc | benefits             | 2023-06-05T07:35:43Z      | 619                |

- Total of 5 calls
- 1 call with `call_category = 'n/a'`

The percentage would be `(1 / 5) * 100 = 20.0%`.

---

### SQL Solution (Example in PostgreSQL 14)

```sql
WITH uncategorised_callers AS (
    SELECT COUNT(case_id) AS uncategorised_calls
    FROM callers
    WHERE call_category IS NULL
      OR call_category = 'n/a'
)
SELECT 
    ROUND(
        (100.0 * uncategorised_calls / (SELECT COUNT(case_id) FROM callers))::numeric,
        1
    ) AS uncategorised_call_pct
FROM uncategorised_callers;
