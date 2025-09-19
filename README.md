# Google_Play_store_Reviews_ChatGPT
Data Ingestion and early analysis of ChatGPT reviews sample data from Google Play store.

This is a dataset of reviews for ChatGPT in Google play store.

## Metadata
Contains 1000 Descriptive data for reviews, gained by using google_play_scraper most_relevant sort.

- Source: Google Play Store
- App: ChatGPT (`com.openai.chatgpt`)
- Collection method: google_play_scraper
- Sort mode: most_relevant
- Sample size: 1000 reviews
- Collection date: Sept 2025
- Language: English (default)

Dataset Fields
```python
{
    "reviewId": "<class 'str'>",
    "userName": "<class 'str'>",
    "userImage": "<class 'str'>",
    "content": "<class 'str'>",
    "score": "<class 'int'>",
    "thumbsUpCount": "<class 'int'>",
    "reviewCreatedVersion": "<class 'str'>",
    "at": "<class 'datetime.datetime'>",
    "replyContent": "<class 'NoneType'>",
    "repliedAt": "<class 'NoneType'>",
    "appVersion": "<class 'str'>"
}
```
Most replyContent and repliedAt values are null.

## EDA

### Score distribution
Discover the score distribution for top relevant reviews

![g1](score_distribution.png?v=2025-09-10)

Here is the box plot for score distribution. Mean score: 2.76, Median score: 3.0. STD: 1.56, 27% of reviews have 1 star, while 22% have 5 star.

Distribution is bimodal, with a heavy cluster at 1 star and 5 star.

### Time data
Show reviews counts and average scores over time.

![g2](time_data.png?v=2025-09-10)

Review counts peaked in August 2025. Average score dropped from 4.1 in Jan 2025 to 3.2 by Feb 2025

### Scores grouped data
Show average thumbs up by scores and total count of scores

![g3](score_thumbs_up_count.png?v=2025-09-10)

5 score reviews gain much more thumbs up by other users than other scores.
1 score reviews have largest data amount(27%) but least amount of thumbs up count.

## Early Insight for data quality
### Pro:
All score categories (1–5) are well covered; no extreme imbalance.
High user engament, contains reviews with lots of thumbs-up.

### Con:
Heavy skew toward very recent months due to “most relevant” sort. This may underrepresent older feedback.
5 star reviews attract disproportionately high “thumbs up,” suggesting agreement bias in positive reviews.

## Product-centric Analysis
### Pain Point Analysis on Low-Star Reviews
#### Method
- Filtered reviews with ratings < 3 stars.
- Applied text cleaning (lowercasing, punctuation removal, stopword removal).
- Counted word/bigram frequencies to extract common complaint phrases.

#### Findings
![g4](complaints_words.png)

#### Insight
Operation time, image generation and some availability issues are mainly reported.

### Version-Based Trends
#### Method
- Computed average star rating per version.
- Tracked frequency of complaint phrases across versions on reviews with rating < 3 stars.
- Targeting versions with review count > 50.

#### Findings
![g5](version_score.png)
- v1.2025.205 -> v1.2025.210: Average score drops from 3.7 to 3.2.
- v1.2025.210 -> v1.2025.245: Average score increases continuously to 4.4.

![g6](complaints_trend.png)
- complaints about doesn't work increases in version v1.2025.203.
- wasting time and please fix complaints increase in later versions.

#### Insight
Operating time and doesn't work issues are complaints more on version with lower average score.

### Feature-Specific Insights
#### Method
- Defined feature categories in: login, subscription, image gen, voice mode and performance
- Tagged reviews mentioning these features via keyword matching
- Compared average rating and percentage of low-star reviews per feature.

#### Findings

|              |     Login |   Subscription |   Image Generation |   Voice Mode |   Performance |
|:-------------|----------:|---------------:|-------------------:|-------------:|--------------:|
| mentions     | 178       |     3378       |         3060       |   1848       |    1534       |
| avg_score    |   2.09551 |        2.98757 |            3.10065 |      3.46374 |       3.71382 |
| low_star_pct |  72.4719  |       43.0136  |           38.1699  |     30.1407  |      24.6415  |

#### Insight
Login and Subscription are major sentiment issues.
