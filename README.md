
---

# Social Media Data Analysis

This project analyzes social media engagement, trends, sentiment, and user behavior using a sample dataset of posts and users. The analysis includes the following key objectives:

- **Hashtag Trends**: Identify the most popular hashtags based on their frequency in posts.
- **Engagement by Age Group**: Analyze user engagement by comparing likes and retweets across different age groups.
- **Sentiment vs Engagement**: Explore the relationship between sentiment and user engagement.
- **Top Verified Users by Reach**: Identify the most influential verified users based on total reach (likes + retweets).

## Dataset

### 1. `users.csv`
Contains information about users who posted on the platform.

| Column Name  | Data Type | Description                      |
|--------------|-----------|----------------------------------|
| UserID       | Integer   | Unique user ID                  |
| Username     | String    | User's handle                   |
| AgeGroup     | String    | Age category (Teen, Adult, Senior) |
| Country      | String    | Country of residence            |
| Verified     | Boolean   | Whether the account is verified |

### 2. `posts.csv`
Contains data on individual posts made by users.

| Column Name    | Data Type | Description                           |
|----------------|-----------|---------------------------------------|
| PostID         | Integer   | Unique post ID                        |
| UserID         | Integer   | ID of the user who posted             |
| Content        | String    | Text content of the post              |
| Timestamp      | String    | Date and time the post was made       |
| Likes          | Integer   | Number of likes on the post          |
| Retweets       | Integer   | Number of shares/retweets            |
| Hashtags       | String    | Comma-separated hashtags used in the post |
| SentimentScore | Float     | Sentiment score (-1 to 1, where -1 is most negative) |

## Project Overview

The project involves reading the `posts.csv` and `users.csv` datasets, cleaning and processing the data, and performing the following analyses:

### 1. Hashtag Trends
- **Task**: Extract individual hashtags from posts and count their frequency.
- **Objective**: Identify the top 10 most-used hashtags and their counts.

### 2. Engagement by Age Group
- **Task**: Merge `posts.csv` and `users.csv` on `UserID`, and group posts by age group to calculate average likes and retweets.
- **Objective**: Analyze engagement across different age groups and rank them based on overall engagement.

### 3. Sentiment vs Engagement
- **Task**: Categorize posts into Positive, Neutral, or Negative sentiment based on the `SentimentScore`, and group the data to calculate average likes and retweets for each sentiment.
- **Objective**: Understand how sentiment affects user engagement.

### 4. Top Verified Users by Reach
- **Task**: Filter verified users, calculate their total reach (Likes + Retweets), and return the top 5 users with the highest total reach.
- **Objective**: Identify the most influential verified users based on their engagement.

---

## Installation

### Requirements

Ensure the following libraries are installed:

- pandas
- matplotlib (for visualizations)
- seaborn (for visualizations)

You can install these libraries using pip:

```bash
pip install pandas matplotlib seaborn
```

---

## Task 1: Hashtag Trends

### Code:

```python
import pandas as pd

# Load the dataset
posts_df = pd.read_csv('input/posts.csv')

# Extract hashtags and count frequency
hashtags = posts_df['Hashtags'].str.split(',', expand=True).stack().value_counts()

# Get the top 10 most used hashtags
top_hashtags = hashtags.head(10)

# Print results
print(top_hashtags)
```

### Sample Input:

`posts.csv` (example rows):

| PostID | UserID | Content                       | Timestamp           | Likes | Retweets | Hashtags       | SentimentScore |
|--------|--------|-------------------------------|---------------------|-------|----------|----------------|----------------|
| 101    | 1      | Loving the new update!         | 2023-10-05 14:20:00 | 120   | 45       | #tech,#innovation | 0.8            |
| 102    | 2      | This app keeps crashing.       | 2023-10-05 15:00:00 | 5     | 1        | #fail          | -0.7           |
| 103    | 3      | Just another day...            | 2023-10-05 16:30:00 | 15    | 3        | #mood          | 0.0            |

### Sample Output:

| Hashtag        | Count |
|----------------|-------|
| #tech          | 120   |
| #mood          | 98    |
| #fail          | 85    |
| #design        | 70    |
| #UX            | 60    |
| #cleanUI       | 50    |
| #AI            | 45    |
| #love          | 42    |
| #social        | 39    |
| #bug           | 35    |

---

## Task 2: Engagement by Age Group

### Code:

```python
# Load users dataset
users_df = pd.read_csv('input/users.csv')

# Merge datasets on UserID
merged_df = pd.merge(posts_df, users_df, on="UserID")

# Group by AgeGroup and calculate average likes and retweets
engagement_by_age = merged_df.groupby('AgeGroup').agg(
    avg_likes=('Likes', 'mean'),
    avg_retweets=('Retweets', 'mean')
).reset_index()

# Rank the groups based on total engagement (likes + retweets)
engagement_by_age['total_engagement'] = engagement_by_age['avg_likes'] + engagement_by_age['avg_retweets']
engagement_by_age = engagement_by_age.sort_values(by='total_engagement', ascending=False)

# Print results
print(engagement_by_age[['AgeGroup', 'avg_likes', 'avg_retweets']])
```

### Sample Input:

`users.csv` (example rows):

| UserID | Username      | AgeGroup | Country | Verified |
|--------|---------------|----------|---------|----------|
| 1      | @techie42     | Adult    | US      | True     |
| 2      | @critic99     | Senior   | UK      | False    |
| 3      | @daily_vibes  | Teen     | India   | False    |

`posts.csv` (example rows):

| PostID | UserID | Content                       | Timestamp           | Likes | Retweets | Hashtags       | SentimentScore |
|--------|--------|-------------------------------|---------------------|-------|----------|----------------|----------------|
| 101    | 1      | Loving the new update!         | 2023-10-05 14:20:00 | 120   | 45       | #tech,#innovation | 0.8            |
| 102    | 2      | This app keeps crashing.       | 2023-10-05 15:00:00 | 5     | 1        | #fail          | -0.7           |

### Sample Output:

| Age Group | Avg Likes | Avg Retweets |
|-----------|-----------|--------------|
| Adult     | 67.3      | 25.2         |
| Teen      | 22.0      | 5.6          |
| Senior    | 9.2       | 1.3          |

---

## Task 3: Sentiment vs Engagement

### Code:

```python
# Categorize posts by sentiment
merged_df['SentimentCategory'] = merged_df['SentimentScore'].apply(
    lambda x: 'Positive' if x > 0 else ('Negative' if x < 0 else 'Neutral')
)

# Group by sentiment category and calculate average likes and retweets
sentiment_engagement = merged_df.groupby('SentimentCategory').agg(
    avg_likes=('Likes', 'mean'),
    avg_retweets=('Retweets', 'mean')
).reset_index()

# Print results
print(sentiment_engagement)
```

### Sample Input:

Same as the previous tasks (`posts.csv` and `users.csv`).

### Sample Output:

| Sentiment | Avg Likes | Avg Retweets |
|-----------|-----------|--------------|
| Positive  | 85.6      | 32.3         |
| Neutral   | 27.1      | 10.4         |
| Negative  | 13.6      | 4.7          |

---

## Task 4: Top Verified Users by Reach

### Code:

```python
# Filter verified users
verified_users = merged_df[merged_df['Verified'] == True]

# Calculate total reach (Likes + Retweets)
verified_users['TotalReach'] = verified_users['Likes'] + verified_users['Retweets']

# Find top 5 users with the highest total reach
top_verified_users = verified_users[['Username', 'TotalReach']].sort_values(by='TotalReach', ascending=False).head(5)

# Print results
print(top_verified_users)
```

### Sample Input:

Same as the previous tasks (`posts.csv` and `users.csv`).

### Sample Output:

| Username      | Total Reach |
|---------------|-------------|
| @social_queen | 845         |
| @techie42     | 822         |
| @rage_user    | 577         |
| @designer_dan | 566         |

---

## License

This project is licensed under the MIT License.

---

You can save this as `README.md` to provide a detailed explanation of how to run the analysis and what each task does. Let me know if you'd like to further enhance this!
