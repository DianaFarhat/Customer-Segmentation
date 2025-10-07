# 🧭 User Engagement & Cadence Segmentation System

### Overview
This system classifies users into behavioral **cohorts** based on how **deeply** and **frequently** they engage, combined with their **subscription** and **account age** status.

It’s designed for **A local newspaper's marketing and audience teams** to identify key user groups for retention, conversion, and reactivation campaigns — without drowning in 40+ micro-segments.

---

## 🧩 Core Inputs

| Source | Description |
|--------|--------------|
| **`eng_df`** | Base engagement dataset with weekly user-level stats (visits, actions, time, etc.). |
| **`merged`** | Enriched dataset that adds Subscription and New Account info. |
| **`dfu`** | 8-week rolling user baseline (depth & engagement score). |
| **`cad`** | User cadence labeling table (activity rhythm + “New Accounts” info). |

All scripts run in **Google Colab** and output cleaned `.xlsx` reports into `/content/cleaned/`.

---

## ⚙️ Processing Steps

1. **Engagement Scoring**  
   - Uses a **robust MAD-based repair** on visit duration to clean outliers.  
   - Builds a weighted engagement score from:  
     - Actions per visit  
     - Average time per visit  
     - Visit volume  

2. **8-Week User Baseline**  
   - Aggregates engagement metrics over each user’s last 8 active weeks.  
   - Computes median repaired visit time, weighted action rate, and total visits.

3. **Benchmarking (Depth)**  
   - Users are benchmarked by segment (Subscriber vs Non-Subscriber).  
   - Each receives a **Depth Band**:  
     `High Depth`, `Good Depth`, `Light Depth`, or `Quick Scan`.

4. **Cadence Labeling**  
   - Based on visit rhythm (`AvgDaysBetween`, `WeeksActive`, `Visits`).  
   - Labels include:  
     `Daily`, `Weekly`, `Biweekly`, `Sporadic`, `Binge`, `Inactive`, etc.  
   - Integrates **“New Accounts”** info to identify `Fresh Joiners` and `Rising Newbies`.

5. **Final User Summary**  
   - Merges engagement depth, cadence, and subscription status.  
   - Outputs one row per user with all key behavioral fields.

---

## 🧠 Cohort Model

Each user is assigned to one of **9 key cohorts** — simple, mutually exclusive, and marketing-actionable.

| # | Cohort | Who They Are | Marketing Focus |
|---|---------|--------------|-----------------|
| 1 | **Fresh Joiners (<7d)** | New accounts in their first week. | Welcome and onboarding content. |
| 2 | **Rising Newbies (8–20d)** | Second/third-week users. | Build habit, encourage deeper usage. |
| 3 | **Core Loyalists** | Subscribers with **high depth** and **high cadence**. | Retain and reward. |
| 4 | **Deep Periodic Readers** | Subscribers with **high depth** but **less frequent** visits. | Maintain engagement with premium, long-form content. |
| 5 | **Active Skimmers** | Subscribers with **low depth**, **high cadence**. | Serve highlights, daily digests, short-form content. |
| 6 | **At-Risk Subscribers** | Subscribers with **low depth** and **low cadence**. | Reactivation and renewal reminders. |
| 7 | **High-Potential Non-Subs** | Non-subs with **high depth + high cadence**. | Conversion campaigns, paywall nudges. |
| 8 | **Exploring Non-Subs** | Non-subs with **high cadence**, **low depth**. | Educate on benefits, encourage deeper reading. |
| 9 | **Dormant / Lapsed** | Inactive or very low-cadence users. | Win-back or reactivation campaigns. |

---

## 🎨 Visualization

A simple cohort distribution chart helps monitor user composition:

```python
cohort_counts = summary["Cohort"].value_counts().sort_values(ascending=True)
plt.figure(figsize=(10,6))
plt.barh(cohort_counts.index, cohort_counts.values)
plt.title("User Cohort Distribution")
plt.xlabel("Number of Users")
plt.ylabel("Cohort")
plt.tight_layout()
plt.show()
