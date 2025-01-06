### Get the Second Most Recent Activity

Table: UserActivity
```
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| username      | varchar |
| activity      | varchar |
| startDate     | Date    |
| endDate       | Date    |
+---------------+---------+
```
This table may contain duplicate rows.
This table contains information about the activity performed by each user in a period of time.
A person with username performed an activity from startDate to endDate.
 
Write a solution to show the second most recent activity of each user.

If the user only has one activity, return that one. A user cannot perform more than one activity at the same time.

Return the result table in any order.

The result format is in the following example.

 

Example 1:

Input: 
UserActivity table:
```
+------------+--------------+-------------+-------------+
| username   | activity     | startDate   | endDate     |
+------------+--------------+-------------+-------------+
| Alice      | Travel       | 2020-02-12  | 2020-02-20  |
| Alice      | Dancing      | 2020-02-21  | 2020-02-23  |
| Alice      | Travel       | 2020-02-24  | 2020-02-28  |
| Bob        | Travel       | 2020-02-11  | 2020-02-18  |
+------------+--------------+-------------+-------------+
```
Output: 
```
+------------+--------------+-------------+-------------+
| username   | activity     | startDate   | endDate     |
+------------+--------------+-------------+-------------+
| Alice      | Dancing      | 2020-02-21  | 2020-02-23  |
| Bob        | Travel       | 2020-02-11  | 2020-02-18  |
+------------+--------------+-------------+-------------+
```
Explanation: 
The most recent activity of Alice is Travel from 2020-02-24 to 2020-02-28, before that she was dancing from 2020-02-21 to 2020-02-23.
Bob only has one record, we just take that one.

---

### Thought Process
1. We need to determine the second most recent activity for each user.
2. For users with only one activity, return that activity.
3. Use a window function to rank activities based on their start date in descending order for each user.
4. Count the total number of activities per user to determine users with only one activity.
5. Filter the results to include either the second most recent activity (rank = 2) or the only activity for users with one activity.

---

### Query

```sql
with cte as (
    select *, 
        dense_rank() over(partition by username order by startDate desc) as r,
        count(*) over(partition by username) as total_activities
    from UserActivity
)

select username, activity, startDate, endDate 
from cte 
where r = 2 or total_activities = 1;
