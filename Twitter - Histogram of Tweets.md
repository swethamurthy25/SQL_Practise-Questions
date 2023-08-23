
Assume you're given a table of Twitter tweet data, and write a query to obtain a histogram of tweets posted per user in 2022. Output the tweet counts per user 
as the bucket and the number of Twitter users who fall into that bucket.

In other words, group the users by the number of tweets they posted in 2022 and count the number of users in each group.

<img width="551" alt="image" src="https://github.com/swethamurthy25/SQL_Practise-Questions/assets/112581595/22249f52-5795-41bc-9a38-c796ef60f6f6">

<img width="220" alt="image" src="https://github.com/swethamurthy25/SQL_Practise-Questions/assets/112581595/8e456ed9-1dd2-4eaa-a817-a125556fd74f">


### Solution 1

let's forget about the Tweet activity histogram for a second, and solve a simpler sub-problem:

First, we need to find the number of tweets posted by each user in 2022 - We need to use COUNT & GROUP BY Function for this

```sql
SELECT user_id, COUNT(tweet_id) AS tweet_count_per_user
FROM tweets
WHERE tweet_date BETWEEN '2022-01-01' AND '2022-12-31'
GROUP BY user_id;
```

Output:

<img width="329" alt="image" src="https://github.com/swethamurthy25/SQL_Practise-Questions/assets/112581595/b4cf5fae-7503-40e2-bea2-53ec07f06838">

Based on the output, we can infer that in the year 2022, user 111 has posted two tweets, while users 148 and 254 have only posted one tweet each.

Next, we use the query above as a subquery, and then we use the tweet_count_per_user field as the tweet bucket and retrieve the number of users.

```sql
SELECT tweet_count_per_user AS tweet_bucket,
COUNT(user_id) AS users_num
FROM (
  SELECT user_id, COUNT(tweet_id) AS tweet_count_per_user
  FROM tweets
  WHERE tweet_date BETWEEN '2022-01-01' AND '2022-12-31'
  GROUP BY user_id) AS total_tweets
GROUP BY tweet_count_per_user;
```

<img width="368" alt="image" src="https://github.com/swethamurthy25/SQL_Practise-Questions/assets/112581595/f6a89556-7507-415a-95df-875ea13860db">

### Solution 2

We can solve this by using CTE also -- Use the sub-query as the CTE query

```sql
WITH total_tweets AS (
  SELECT user_id, COUNT(tweet_id) AS tweet_count_per_user
  FROM tweets
  WHERE tweet_date BETWEEN '2022-01-01' AND '2022-12-31'
  GROUP BY user_id)
  
SELECT * FROM total_tweets;
```
<img width="289" alt="image" src="https://github.com/swethamurthy25/SQL_Practise-Questions/assets/112581595/70f95996-8eaf-4e3a-a855-c98f5d538cc1">

Now using the above CTE, Just count the user ID and group it by tweet_count_per_user

```sql
WITH total_tweets AS (
  SELECT user_id, COUNT(tweet_id) AS tweet_count_per_user
  FROM tweets
  WHERE tweet_date BETWEEN '2022-01-01' AND '2022-12-31'
  GROUP BY user_id)
  
SELECT tweet_count_per_user AS tweet_bucket,
COUNT(user_id) AS users_num
FROM total_tweets
GROUP BY tweet_count_per_user;
```

<img width="357" alt="image" src="https://github.com/swethamurthy25/SQL_Practise-Questions/assets/112581595/37d0b009-7787-42a0-beee-5238b5369151">


