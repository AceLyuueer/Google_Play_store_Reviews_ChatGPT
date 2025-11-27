# Google Play Rating–Sentiment Analysis  
A research project that performs **3-class sentiment analysis** on Google Play reviews and uses **visual and statistical correlation** to understand how user sentiment trends relate to rating changes over time.

---

## 📑 Table of Contents
- [Overview](#overview)
- [Research Goals](#research-goals)
- [Problem Statement](#problem-statement)
- [Methodology](#methodology)
  - [Data Collection](#data-collection)
  - [Sentiment Classification (3-Class Model)](#sentiment-classification-3-class-model)
  - [Aggregation](#aggregation)
  - [Visualization](#visualization)
  - [Statistical Correlation](#statistical-correlation)
- [Repository Structure](#repository-structure)
- [How to Run](#how-to-run)
- [Results](#results)
- [Limitations](#limitations)
- [Future Work](#future-work)
- [License](#license)

---

## 🧭 Overview
This project investigates how **user sentiment** in Google Play reviews correlates with **rating trends** over time.  
By classifying each review into **positive**, **neutral**, or **negative**, and comparing aggregated sentiment with rating history, the project aims to reveal:

- what drives rating increases or drops  
- emotional reactions to app updates  
- early warning signs of negative sentiment  
- divergence between ratings and user mood  
- long-term sentiment trends  

This repository contains scripts and notebooks for data collection, sentiment modeling, visualization, and statistical correlation analysis.

---

## 🎯 Research Goals
This project aims to answer:

- How does overall sentiment (positive/neutral/negative) change over time?  
- Do changes in sentiment correlate with rating changes?  
- Does negative sentiment precede rating drops?  
- Do positive sentiment spikes follow successful updates?  
- Are there periods where sentiment and ratings diverge significantly?  

---

## ❗ Problem Statement
Google Play shows rating history but **does not explain why ratings change**.

The major challenges:

- Rating numbers offer no insight into *user emotions*.  
- Review text is long, noisy, and impossible to read manually at scale.  
- Google Play does not provide sentiment data.  
- It's difficult to link rating drops to specific user frustrations.  
- No existing dashboard combines sentiment and rating timelines.  

This research fills that gap by extracting quantifiable sentiment from reviews and correlating it with rating trends.

---

## 🔬 Methodology

### 📥 Data Collection

Google Play Store does not provide a public API for full historical review archives, and the reviews returned are influenced by Google's internal relevance ranking. To maximize coverage, this project collects reviews using multiple sorting modes (`NEWEST`, `MOST_RELEVANT`, and `RATING`) and iteratively follows continuation tokens until no more data is available.

This approach retrieves the **maximum set of reviews currently exposed by the Google Play API**, but older reviews may not be fully available due to platform limitations. As a result, the dataset represents the reviews that are *currently visible* rather than the complete historical archive.

Each review includes:
- Text content  
- Star rating (1–5)  
- Review timestamp  
- App version (if available)  
- Review ID  

---

### 😀 Sentiment Classification (3-Class Model)
Each review is labeled as:

- **Positive**  
- **Neutral**  
- **Negative**

Sentiment is extracted using an LLM (e.g., ChatGPT), ensuring consistency and scalability.

---

### 📊 Aggregation
- Group reviews by day/week/month  
- Calculate counts and proportions of each sentiment  
- Align sentiment timeline with rating timeline  

---

### 📈 Visualization
This project includes:

- Sentiment-over-time line charts  
- Rating trend charts  
- Overlay charts combining both  
- Highlighted periods of divergence  

These visuals help reveal hidden patterns and emotional responses to updates or issues.

---

### 📉 Statistical Correlation
Methods include:

- **Pearson correlation**  
- **Spearman rank correlation**  
- **Lagged correlation** (sentiment leading/following rating changes)  
- **Correlation heatmaps**

This quantifies how strongly sentiment and rating trends move together.

---

## 📁 Repository Structure



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
The drop in v1.2025.210 is strongly associated with an increase in “operation time” and “doesn’t work” complaints, indicating stability and reliability issues

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
- Login issues are the most severe pain point with greatest low_star_pct.
- subscription is the most frequently mentioned complaint with low average ratings.
