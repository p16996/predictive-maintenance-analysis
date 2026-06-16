# Predictive Maintenance — Anomaly Detection Analysis

Real-world analysis of 10,000 manufacturing sensor readings to detect equipment anomalies, identify failure patterns, and generate AI-powered explanations for maintenance teams.

---
## Background

After a career break I built this project to reconnect with hands-on data work. I chose predictive maintenance deliberately — it mirrors the real-time sensor analytics and anomaly detection pipelines I built professionally across global manufacturing sites.

---
## What I built
An end-to-end anomaly detection pipeline that:
- Explores sensor data to identify failure patterns
- Detects anomalies using two independent statistical methods
- Compares method results to identify highest-confidence alerts
- Automatically explains flagged anomalies in plain English using the Claude AI API — making findings actionable for non-technical maintenance engineers
- Calculates input tokens exactly and derives output token budgets from explicit prompt constraints, then logs real token usage and cost from the API response
---
## The interesting problem I ran into
Applying standard anomaly detection thresholds out of the box — z-score of 3 and IQR multiplier of 1.5 — flagged zero anomalies.

After investigating, I found that temperature in this dataset drifts gradually over time rather than spiking abruptly. The default thresholds were sitting outside the entire data range, making them useless for this equipment type.

I tuned the thresholds down (z-score=2, IQR multiplier=0.75) to reflect the actual behavior of the data. This is a real production challenge — thresholds must be calibrated against actual equipment behavior, not applied as universal defaults.

A similar tuning problem showed up in the AI explanation layer: early prompts produced explanations that ran on too long and got cut off mid-sentence. Adding explicit constraints — exact sentence count and a word limit per sentence — fixed this and made the output length predictable enough to budget tokens accurately.

---
## Key findings
**Failure patterns**
- 3.4% of readings resulted in machine failure — rare but costly
- 1.1% of readings exceeded the safe operating threshold of 304K
- Failed machines ran at a consistently higher median temperature (301.6K) vs non-failed machines (300.0K)
- Torque showed stronger correlation with failure (0.191) than air temperature (0.083) — mechanical stress is a more reliable failure indicator than thermal conditions alone
- No single sensor variable strongly predicted failure, suggesting a multivariate model would outperform single-threshold alerting

**Anomaly detection results**
- Z-score (threshold=2): 224 anomalies detected (2.24%)
- IQR (multiplier=0.75): 263 anomalies detected (2.63%)
- 218 readings flagged by both methods — 97% agreement between two independent approaches, representing highest-confidence alerts

**AI-generated explanations**
- Used the Claude API to generate 2-sentence, plain-English explanations for the top 5 highest-confidence anomalies
- Each explanation states what the anomaly indicates and what action a maintenance engineer should take — under 20 words per sentence, validated against the actual output
- Total cost for 5 explanations: under 2 cents, tracked using real token counts from the API response rather than estimates

**Note:**
anomaly detection flags statistically unusual readings independent of failure outcome. 4 of 5 top anomalies had not yet resulted in failure — consistent with the goal of early warning rather than after-the-fact detection. 1 anomaly corresponded to an actual failure, offering preliminary validation of the method's predictive value.

---
## Tools and methods

| Category | Detail |
|---|---|
| Language | Python |
| Data processing | Pandas |
| Visualization | Matplotlib, Plotly |
| Statistical detection | Z-score (SciPy), IQR |
| AI explanations | Anthropic Claude API |
| Environment | Google Colab |

---

## Dataset

AI4I 2020 Predictive Maintenance Dataset
Source: UCI Machine Learning Repository
10,000 sensor readings · Equipment types: L, M, H
Variables: air temperature, process temperature, rotational speed, torque, tool wear, failure indicators
Link : https://www.kaggle.com/datasets/stephanmatzka/predictive-maintenance-dataset-ai4i-2020

---

## How to run

1. Open notebook in Google Colab using the badge below
2. Upload `predictive_maintenance.csv` from UCI repository
3. Add your Anthropic API key to Colab secrets as `ANTHROPIC_API_KEY`
4. Run all cells in order

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/p16996/predictive-maintenance-analysis/blob/main/Predictive_Maintenance.ipynb)

---

**Prathyusha Sathineni**
Senior Data Analyst · PySpark · Databricks · Anomaly Detection
[LinkedIn](https://linkedin.com/in/prathyushasathineni) · [GitHub](https://github.com/p16996)
