# **Histogram of Tweets - Easy**

Assume you're given a table Twitter tweet data, write a query to obtain a histogram of tweets posted per user in 2022. Output the tweet count per user as the bucket and the number of Twitter users who fall into that bucket.

In other words, group the users by the number of tweets they posted in 2022 and count the number of users in each group.

### **`tweets` Table:**

| Column Name | Type |
| --- | --- |
| tweet_id | integer |
| user_id | integer |
| msg | string |
| tweet_date | timestamp |

### **`tweets` Example Input:**

| tweet_id | user_id | msg | tweet_date |
| --- | --- | --- | --- |
| 214252 | 111 | Am considering taking Tesla private at $420. Funding secured. | 12/30/2021 00:00:00 |
| 739252 | 111 | Despite the constant negative press covfefe | 01/01/2022 00:00:00 |
| 846402 | 111 | Following @NickSinghTech on Twitter changed my life! | 02/14/2022 00:00:00 |
| 241425 | 254 | If the salary is so competitive why won’t you tell me what it is? | 03/01/2022 00:00:00 |
| 231574 | 148 | I no longer have a manager. I can't be managed | 03/23/2022 00:00:00 |

### **Example Output:**

| tweet_bucket | users_num |
| --- | --- |
| 1 | 2 |
| 2 | 1 |

### Solution:

**CTE**
```
WITH tweet_peruser AS (
SELECT user_id, COUNT(user_id) AS bucket
FROM tweets
WHERE EXTRACT(YEAR FROM tweet_date) = 2022
GROUP BY user_id)

SELECT DISTINCT bucket, COUNT(user_id)
FROM tweet_peruser
GROUP BY bucket;
```

**SubQuery**
```
SELECT
tweet_count_per_user AS tweet_bucket,
COUNT(user_id) AS users_num
FROM (
SELECT
user_id,
COUNT(tweet_id) AS tweet_count_per_user
FROM tweets
WHERE tweet_date BETWEEN '2022-01-01'
AND '2022-12-31'
GROUP BY user_id) AS total_tweets
GROUP BY tweet_count_per_user;
```