### Get Highest Answer Rate Question

**Table:** SurveyLog
```
+-------------+------+  
| Column Name | Type |  
+-------------+------+  
| id          | int  |  
| action      | ENUM |  
| question_id | int  |  
| answer_id   | int  |  
| q_num       | int  |  
| timestamp   | int  |  
+-------------+------+
```
This table may contain duplicate rows.  
- `action` is an ENUM (category) of the type: "show", "answer", or "skip".  
- Each row of this table indicates the user with `id` has taken an action with the question `question_id` at time `timestamp`.  
- If the action taken by the user is "answer", `answer_id` will contain the id of that answer; otherwise, it will be `null`.  
- `q_num` is the numeral order of the question in the current session.

The **answer rate** for a question is the number of times a user answered the question divided by the number of times a user showed the question.

Write a solution to report the question that has the highest answer rate. If multiple questions have the same maximum answer rate, report the question with the smallest `question_id`.

### Example:

**Input:**  
SurveyLog table:

| id | action | question_id | answer_id | q_num | timestamp |  
|----|--------|-------------|-----------|-------|-----------|  
| 5  | show   | 285         | null      | 1     | 123       |  
| 5  | answer | 285         | 124124    | 1     | 124       |  
| 5  | show   | 369         | null      | 2     | 125       |  
| 5  | skip   | 369         | null      | 2     | 126       |  

**Output:**  

| survey_log |  
|------------|  
| 285        |  

**Explanation:**  
- Question 285 was shown 1 time and answered 1 time. The answer rate of question 285 is 1.0.  
- Question 369 was shown 1 time and was not answered. The answer rate of question 369 is 0.0.  
- Question 285 has the highest answer rate.

---

### Thought Process:

1. **Understand the Requirements:**  
   - For each `question_id`, we need to calculate two metrics:  
     - The number of times the question was shown (`action = 'show'`).  
     - The number of times the question was answered (`action = 'answer'`).
   - Compute the **answer rate** as:  
     `answer_rate = count(answer_id) / count(action='show')`.
   - Identify the question with the highest `answer_rate`. If there is a tie, choose the `question_id` with the smallest value.

2. **Approach:**  
   - Group the data by `question_id`.  
   - Use conditional aggregation to calculate the total counts of `show` and `answer` actions.  
   - Compute the `answer_rate` using these aggregates.  
   - Sort the results by `answer_rate` in descending order and `question_id` in ascending order.  
   - Limit the result to the top 1 entry.

3. **SQL Concepts Used:**  
   - Grouping (`GROUP BY`).  
   - Conditional aggregation with `CASE`.  
   - Sorting (`ORDER BY`).  
   - Limiting results (`LIMIT`).

---

### Query:

```sql
SELECT
  question_id AS survey_log
FROM
  SurveyLog
GROUP BY
  question_id
ORDER BY
  COUNT(CASE WHEN action = 'answer' THEN 1 END) / 
  COUNT(CASE WHEN action = 'show' THEN 1 END) DESC,
  question_id
LIMIT
  1;
```

---

### Explanation of the Query:

1. **Grouping by `question_id`:**  
   - This ensures we calculate metrics for each question separately.

2. **Calculating the Answer Rate:**  
   - `COUNT(CASE WHEN action = 'answer' THEN 1 END)` counts the number of times the question was answered.  
   - `COUNT(CASE WHEN action = 'show' THEN 1 END)` counts the number of times the question was shown.  
   - Dividing these gives the `answer_rate`.

3. **Sorting the Results:**  
   - The `ORDER BY` clause sorts the questions by `answer_rate` in descending order.  
   - If there is a tie in `answer_rate`, it uses `question_id` in ascending order as the tiebreaker.

4. **Limiting the Output:**  
   - The `LIMIT 1` clause ensures only the question with the highest answer rate is returned.

---

### Output:

- For the given example, the query returns:

| survey_log |  
|------------|  
| 285        |
