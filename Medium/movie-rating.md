### Movie Rating
We are given three tables:

#### Table: `Movies`

| Column Name | Type    |
|-------------|---------|
| movie_id    | int     |
| title       | varchar |

- `movie_id` is the primary key.
- Each row represents a movie.

#### Table: `Users`

| Column Name | Type    |
|-------------|---------|
| user_id     | int     |
| name        | varchar |

- `user_id` is the primary key.
- Each row represents a user.

#### Table: `MovieRating`

| Column Name | Type |
|-------------|------|
| movie_id    | int  |
| user_id     | int  |
| rating      | int  |
| created_at  | date |

- `(movie_id, user_id)` is the primary key.
- Each row represents a movie review with its rating and the date the review was created.

### Task
1. **Find the user who has rated the greatest number of movies:**
   - If multiple users are tied, return the lexicographically smallest user name.

2. **Find the movie with the highest average rating in February 2020:**
   - If multiple movies are tied, return the lexicographically smallest movie title.

### Example
#### Input:
**Movies table:**

| movie_id | title    |
|----------|----------|
| 1        | Avengers |
| 2        | Frozen 2 |
| 3        | Joker    |

**Users table:**

| user_id | name   |
|---------|--------|
| 1       | Daniel |
| 2       | Monica |
| 3       | Maria  |
| 4       | James  |

**MovieRating table:**

| movie_id | user_id | rating | created_at |
|----------|---------|--------|------------|
| 1        | 1       | 3      | 2020-01-12 |
| 1        | 2       | 4      | 2020-02-11 |
| 1        | 3       | 2      | 2020-02-12 |
| 1        | 4       | 1      | 2020-01-01 |
| 2        | 1       | 5      | 2020-02-17 |
| 2        | 2       | 2      | 2020-02-01 |
| 2        | 3       | 2      | 2020-03-01 |
| 3        | 1       | 3      | 2020-02-22 |
| 3        | 2       | 4      | 2020-02-25 |

#### Output:

| results  |
|----------|
| Daniel   |
| Frozen 2 |

#### Explanation:
1. **User with Most Ratings:**
   - Daniel and Monica both rated 3 movies.
   - Since "Daniel" is lexicographically smaller, we select him.

2. **Movie with Highest Average Rating in February 2020:**
   - Frozen 2 and Joker both have an average rating of `3.5`.
   - Since "Frozen 2" is lexicographically smaller, we select it.

---

### Thought Process
1. **Finding the User with Most Ratings:**
   - Join `Users` with `MovieRating` on `user_id`.
   - Group by `user_id` and count the ratings.
   - Order by the count of ratings in descending order and then by `name` in ascending order.
   - Use `LIMIT 1` to get the top user.

2. **Finding the Movie with Highest Average Rating in February 2020:**
   - Filter `MovieRating` for reviews created in February 2020.
   - Join `MovieRating` with `Movies` on `movie_id`.
   - Group by `movie_id` and calculate the average rating.
   - Order by average rating in descending order and then by `title` in ascending order.
   - Use `LIMIT 1` to get the top movie.

3. **Combine Results:**
   - Use `UNION ALL` to combine the two queries.

---

### SQL Query
```sql
(
    SELECT u.name AS results
    FROM Users u
    JOIN MovieRating mr
    ON u.user_id = mr.user_id
    GROUP BY u.user_id
    ORDER BY COUNT(rating) DESC, name 
    LIMIT 1
)
UNION ALL
(
    SELECT title AS results
    FROM MovieRating mr
    JOIN Movies m
    ON mr.movie_id = m.movie_id
    WHERE YEAR(created_at) = 2020 AND MONTH(created_at) = 2
    GROUP BY title
    ORDER BY AVG(rating) DESC, title 
    LIMIT 1
);
