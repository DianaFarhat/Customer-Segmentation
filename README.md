# 🗞️ User Engagement, Cadence & Category Intelligence System

### Overview
This system powers **A local newspaper's audience segmentation and engagement analytics**, unifying behavioral, subscription, and content-consumption data into actionable cohorts for marketing, editorial, and product teams.

It identifies **how users engage (depth + frequency)**, **who they are (subscribed/new)**, and **what they love (category focus)** — producing a single, human-readable profile for each user.

---

## 🧩 Core Inputs

| Source | Description |
|--------|--------------|
| **`eng_df`** | Base weekly user-level engagement data (visits, actions, durations). |
| **`merged`** | Enriched data with Subscription & New Account fields. |
| **`dfu`** | 8-week rolling user baseline — engagement intensity & depth benchmark. |
| **`cad`** | Cadence labeling table (rhythm, gaps, and newness). |
| **`cats`** | Category analytics — time spent and focus indicators by content section. |

All scripts run in **Google Colab** and output cleaned `.xlsx` reports into `/content/cleaned/`.

---

## ⚙️ Processing Pipeline

### 1️⃣ Engagement Scoring  
- Cleans raw durations via **MAD-based repair** to remove inflated time.  
- Builds a weighted **Engagement Score** from:
  - Actions per visit  
  - Average visit duration (after repair)  
  - Visit frequency  

### 2️⃣ 8-Week User Baseline  
- Aggregates metrics over each user’s **last 8 active weeks**.  
- Captures engagement medians and visit-weighted means.

### 3️⃣ Benchmarking (Depth)  
- Computes **Depth Band** relative to peers (`High / Good / Light / Quick Scan`).  
- Depth measures *content absorption per session*.

### 4️⃣ Cadence Labeling  
- Uses **AvgDaysBetween**, **WeeksActive**, and **Visits** to classify rhythm:  
  `Daily`, `Weekly`, `Biweekly`, `Sporadic`, `Binge`, `Inactive`.  
- Integrates **“New Accounts”** info to tag:  
  - `Fresh Joiners (<7d)`  
  - `Rising Newbies (8–20d)`

### 5️⃣ Category Intelligence 🗞️  
- Each user’s **category split** (e.g., Politics, Economy, Culture, Sports) is analyzed to identify:  
  - **Favorite Category** → where the user spends most time.  
  - **Time Spent per Category** → normalized as a share of total reading time.  
  - **Focus Index** → how concentrated vs. exploratory their reading behavior is.  
    - *Focused:* ≥70% of time in 1–2 categories.  
    - *Exploratory:* evenly distributed across many sections.

### 6️⃣ Unified User Summary  
- Merges all layers:  
  **Depth + Cadence + Subscription + Newness + Category Affinity**  
- Produces one flat record per user, ready for dashboards and CRM audiences.

---

## 🧠 Cohort Model (9 Segments)

| # | Cohort | Definition | Typical Marketing Focus |
|---|---------|-------------|--------------------------|
| 1 | **Fresh Joiners (<7d)** | New accounts, first-week readers. | Welcome/onboarding flows. |
| 2 | **Rising Newbies (8–20d)** | New users starting to return. | Nurture habit formation. |
| 3 | **Core Loyalists** | Subscribers with **high depth + high cadence**. | Retain & reward. |
| 4 | **Deep Periodic Readers** | Subscribers with **high depth** but visit less frequently. | Weekend content, curated newsletters. |
| 5 | **Active Skimmers** | Subscribers with **low depth + high cadence**. | Short-form, highlight-driven campaigns. |
| 6 | **At-Risk Subscribers** | Subscribers with **low depth + low cadence**. | Reactivation, renewal nudges. |
| 7 | **High-Potential Non-Subs** | Non-subs with **high depth + high cadence**. | Paywall & conversion offers. |
| 8 | **Exploring Non-Subs** | Non-subs with **high cadence**, **low depth**. | Deepen content engagement. |
| 9 | **Dormant / Lapsed** | Very low depth & cadence; inactive. | Win-back campaigns. |

---

## 🗂️ Example Output Columns

| Column | Meaning |
|--------|----------|
| **UserId** | Unique user identifier |
| **Subscribed** | Boolean or tier |
| **NewAccountLabel** | Fresh Joiner / Rising Newbie / — |
| **DepthBand** | High / Good / Light / Quick Scan |
| **CadenceLabel** | Daily / Weekly / Sporadic etc. |
| **EngagementScore_user** | Weighted score of actions, time, visits |
| **FavoriteCategory** | Category with max total time |
| **CategoryFocusIndex** | % of time in top 1–2 sections |
| **FocusType** | “Focused” or “Exploratory” |
| **Cohort** | Final segment label |

---

## 📊 Visualization

### Cohort Distribution
```python
cohort_counts = summary["Cohort"].value_counts().sort_values(ascending=True)
plt.figure(figsize=(10,6))
plt.barh(cohort_counts.index, cohort_counts.values, color="#0077b6")
plt.title("User Cohort Distribution", fontsize=16)
plt.xlabel("Number of Users")
plt.tight_layout()
plt.show()
