# Instagram Analytics: Data Mining & Visualisation

A data mining project that uses **clustering**, **classification** and **association rule mining** in **WEKA** (with supporting visualisations in **Tableau**) to analyse post performance on a synthetic Instagram dataset.

> Coursework for *Data Mining & Visualisation* (ACCA7015), MSc.
> Author: **Teja Vamsi Devalla**

---

## Table of Contents

- [Overview](#overview)
- [Dataset](#dataset)
- [Repository Structure](#repository-structure)
- [Methodology](#methodology)
- [Business Questions & Results](#business-questions--results)
- [Tools Used](#tools-used)
- [How to Reproduce](#how-to-reproduce)
- [Limitations](#limitations)
- [Future Work](#future-work)
- [Acknowledgements](#acknowledgements)

---

## Overview

Creators and brands often post without knowing how a piece of content will perform. This project applies three data mining techniques to Instagram post data to answer three practical business questions:

| # | Business Question | Technique |
|---|-------------------|-----------|
| 1 | Which posts perform best, and what should creators do before posting? | Clustering |
| 2 | Can we predict a post's performance before it is published? | Classification |
| 3 | Which feature combinations are linked to low-performing posts and should be avoided? | Association Rule Mining |

---

## Dataset

- **Name:** Instagram Analytics Dataset
- **Source:** [Kaggle: kundanbedmutha/instagram-analytics-dataset](https://www.kaggle.com/datasets/kundanbedmutha/instagram-analytics-dataset)
- **Size:** 29,999 posts × 23 attributes, across 20 accounts
- **Nature:** Synthetic data modelled on real Instagram insights, so no real user data or privacy concerns. No missing values.
- **Target label:** `performance_bucket_label` (low / medium / high / viral)

**Attribute groups**

| Group | Attributes |
|-------|-----------|
| Identification | `post_id`, `account_id` |
| Account | `account_type` (brand/creator), `follower_count` |
| Content | `media_type` (reel/image/carousel), `content_category` (10 categories), `has_call_to_action`, `caption_length`, `hashtags_count` |
| Timing | `post_datetime`, `post_date`, `post_hour`, `day_of_week` |
| Engagement | `likes`, `comments`, `shares`, `saves` |
| Reach | `reach`, `impressions`, `traffic_source` |
| Performance | `engagement_rate`, `followers_gained`, `performance_bucket_label` |

---

## Repository Structure

```
.
├── README.md
├── data/
│   ├── Instagram_Analytics.csv                 # Original dataset (29,999 rows × 23 columns)
│   └── clustering_Instagram_Analytics.arff     # Preprocessed WEKA file used for clustering
└── reports/
    ├── Data_Mining_Report.docx                 # Full project report
    └── DMV_assignment1_summary_document.docx   # Dataset selection & suitability summary
```

- `Instagram_Analytics.csv` is the raw data, converted to ARFF for use in WEKA.
- `clustering_Instagram_Analytics.arff` contains only the high-performing posts after filtering, normalisation and nominal-to-binary conversion (see Exploration 1).

---

## Methodology

### Preprocessing (all explorations)
- Converted CSV to ARFF so WEKA recognises attribute names and types.
- Removed non-informative attributes (`post_id`, `account_id`, `post_datetime`, `post_date`).
- Applied the WEKA `Normalize` and `NominalToBinary` filters where needed.

### Exploration-specific preprocessing

| Exploration | Preprocessing |
|-------------|---------------|
| 1. Clustering | Kept only **high-performing** posts (filtered with `RemoveWithValues` on likes, followers gained and performance label), dropped those outcome columns to avoid bias, then normalised and binarised |
| 2. Classification | Removed attributes **not known before posting** (likes, reach, impressions, engagement rate, traffic source, etc.) to prevent leakage; normalised and binarised features |
| 3. Association Rules | Kept only **low-performing** posts, dropped outcome columns, then discretised numeric attributes |

---

## Business Questions & Results

### 1. Clustering: what do high-performing posts look like?

| Setting | Detail |
|---------|--------|
| Models compared | SimpleKMeans, EM, Hierarchical (agglomerative) |
| Final model | **SimpleKMeans** (k = 10, Euclidean distance, 500 max iterations, seed 10) |
| Metrics | WCSS, log-likelihood (EM), dendrogram (hierarchical), interpretability |
| Testing | 75:25 percentage split |

**Why SimpleKMeans:** clear, well-separated, interpretable clusters (each post belongs to exactly one cluster) and faster than the alternatives.

**Example insight:** Brands posting **reels** in **food, fashion and travel** are recommended to post on **Wednesdays** (Cluster 0).

### 2. Classification: can we predict performance before posting?

| Setting | Detail |
|---------|--------|
| Models compared | J48, Random Forest, Random Tree |
| Final model | **Random Forest** |
| Metrics | Accuracy, precision, recall, F1-score, confusion matrix |
| Testing | 10-fold cross-validation |

**Result:** Random Forest performed best of the three, but overall accuracy was low (**7,589 / 29,999 correctly classified, about 25.3%**, roughly chance level for four classes). This suggests pre-posting features alone are not enough to reliably predict performance on this dataset, so the model is better suited to guiding recommendations than to automated decisions.

### 3. Association Rules: what should creators avoid?

| Setting | Detail |
|---------|--------|
| Models compared | Apriori, Filtered Associator |
| Final model | **Apriori** |
| Parameters | min support 0.05, min confidence 0.60, min lift 1.20, max rule length 4 |
| Metrics | Support, confidence |

**Key finding:** Low engagement on one metric tends to coincide with low engagement on the others. For example, low comments + low shares → low saves (and the reverse combinations), with several rules reaching confidence of 1.00.

---

## Tools Used

- **WEKA**: preprocessing, clustering, classification, association rule mining
- **Tableau**: exploratory visualisations (media type vs reach, content category vs engagement rate, engagement metrics vs target)
- **Microsoft Word**: reporting

---

## How to Reproduce

1. Install [WEKA](https://ml.cms.waikato.ac.nz/weka/).
2. Open WEKA Explorer → **Preprocess** → **Open file** and load `data/Instagram_Analytics.csv` (or save it as ARFF first).
3. Apply the preprocessing steps for the exploration you want to reproduce (see [Methodology](#methodology) and the full report).
4. For clustering, you can load `data/clustering_Instagram_Analytics.arff` directly, which is already filtered and encoded, then:
   - **Cluster** tab → `SimpleKMeans` with the parameters above.
5. For classification, use the **Classify** tab with `RandomForest` and 10-fold cross-validation.
6. For association rules, discretise the data, then use the **Associate** tab with `Apriori` and the parameters above.

Full details and screenshots are in [`reports/Data_Mining_Report.docx`](reports/Data_Mining_Report.docx).

---

## Limitations

- The data is **synthetic**, so patterns may not reflect real Instagram behaviour.
- The classification model's accuracy is low and not suitable for real-world deployment.
- Clustering and association rules are descriptive, so they show correlation, not causation.

---

## Future Work

- Test on **real** social media data.
- Add richer features such as audience sentiment, emotion and psychology-related signals.
- Try larger datasets and additional features to improve classification performance.

---

## Acknowledgements

- Dataset by [kundanbedmutha](https://www.kaggle.com/datasets/kundanbedmutha/instagram-analytics-dataset) on Kaggle.
- Module: Data Mining & Visualisation (ACCA7015).

## Academic Note

This repository is shared for portfolio and learning purposes. If you are a student on a similar module, please use it for reference only and do not submit it as your own work.
