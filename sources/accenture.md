# Accenture — Data Scientist (Oct 2019 – Apr 2022, Prague)

Reference notes for talking about my time at Accenture. Joined as part of a team standing up a Data Lake + Azure Machine Learning platform, then built several forecasting/classification models on top of it.

---

## 1. Data Lake + Azure ML Platform Setup

**What:** Part of the team that set up the Data Lake and Azure Machine Learning product/platform.

**Why:** Foundational infrastructure needed before any of the forecasting/prediction models below could be built and deployed.

**Tools:** Azure Machine Learning, Data Lake.

---

## 2. Internet Bandwidth Utilization Forecasting

**What:** A large-scale time series forecasting model predicting internet bandwidth utilization, built in cooperation with a senior colleague. Handled 1,000+ individual time series in parallel.

**Why:** Bandwidth capacity planning — forecasting utilization ahead of time to inform infrastructure decisions.

**How:** Facebook Prophet for the forecasting models, deployed and served via Azure Machine Learning.

**Tools:** Facebook Prophet, Azure Machine Learning, multi-series (1,000+) time series forecasting.

---

## 3. Short-Term IT Event Prediction (Beta)

**What:** A beta model predicting internal IT-related events roughly 24 hours ahead.

**Why:** Early-warning signal for operational/IT events, to enable proactive rather than reactive response.

**Tools:** CatBoost.

---

## 4. Graph Database on Gremlin/Cosmos DB (Beta)

**What:** A beta graph database built on the Gremlin API on top of Azure Cosmos DB.

**Why:** Goal was enabling graph-based visualization and centroid/cluster visualization of enterprise data relationships.

**Tools:** Gremlin API, Azure Cosmos DB.

---

## Tech Stack Summary

**Forecasting/ML:** Facebook Prophet, CatBoost, multi-series time series forecasting at scale (1,000+ series).
**Platform/Infra:** Azure Machine Learning, Data Lake, Azure Cosmos DB (Gremlin API / graph database).
**Domain:** Enterprise IT infrastructure — network capacity planning, IT operations event prediction, enterprise data relationship visualization.