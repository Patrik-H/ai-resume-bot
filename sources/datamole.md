# Datamole — Senior Data Scientist / Lead Project Engineer (May 2022 – Feb 2025, Prague)

Reference notes for talking about my time at Datamole, a data science / ML consultancy. I worked across multiple client engagements — agriculture/robotics, food-processing equipment, and transportation/EV fleets — moving from individual contributor to lead engineer on client projects.

---

## 1. Bayesian Optimization — Surrogate Model R&D

**What:** Implemented and benchmarked several surrogate-model approaches for Bayesian optimization:
- Full-rank Gaussian Process
- Sparse GP (tested for performance/scalability trade-offs vs. full-rank)
- A custom implementation of TPE (Tree-Structured Parzen Estimator)
- A custom Bayesian GLM

**Why:** Exploring which surrogate model gave the best accuracy/scalability trade-off for optimization problems on client data. (These specific models were R&D — not the ones that ultimately shipped to production.)

**Tools:** Bayesian programming, Gaussian Processes, TPE, GLMs.

---

## 2. Root-Cause Investigation & Model Fix — Agri-Robotics Client

**What:** Deep-dive investigation into "underperforming" client sites, tracing the issue back to a data pipeline problem rather than a modeling problem.

**Why:** The client's optimization models were performing inconsistently across sites, and the initial hypothesis (model quality) turned out to be wrong — the real cause was upstream data.

**What I found:** A calculation bug on the client's own systems, in a submodel feeding the optimization pipeline. It was affecting a large share of the client's active hardware fleet. I built and shipped a corrected model as an interim fix while the client worked on a hardware/software-level correction.

**Impact:** Deployed the fix starting at a pilot scale, then rolled out to over 1,000 sites within a few months — this was the project I'm proudest of from a "debugging under pressure, with real business impact" standpoint.

**Tools:** Time-series analysis, root-cause/data-quality debugging, production model deployment.

---

## 3. Feasibility Study — Food-Processing Equipment Client (Lead Engineer)

**What:** Took over as Lead Project Engineer on a new client engagement. Delivered a feasibility study proposing:
- A control-loop design draft
- A predictive model
- An estimate of potential reduction in production volume variance

**Why:** Client wanted to evaluate whether a smarter control/prediction approach could meaningfully reduce variance in their process before committing to a full build.

**Tools:** Process modeling, control-loop design, predictive modeling.

---

## 4. Transportation / EV Fleet Client — Two Engagements

**Engagement 1 (as lead engineer):**
- Delivered a proof-of-concept to detect sudden drops in EV efficiency.
- Investigation found the "drops" were actually a data-quality issue (incorrect vehicle-capacity logs on the client side) rather than a real efficiency problem — another case of tracing a modeling symptom back to its data root cause.
- Also analyzed battery degradation by estimating actual capacity change over time.

**Engagement 2 (delivered model):**
- Built and delivered a production EV efficiency estimation model using features like mileage, battery age, distance logs, temperature, and rolling 7-day efficiency/temperature averages, tailored per vehicle type.
- Deployed as a live endpoint on AWS SageMaker.
- Handed the project over cleanly to the client's internal data science team.

**Also scoped:** A short feasibility study for a separate transportation client problem — predicting battery life and charging efficiency for planning purposes.

**Tools:** Feature engineering for time-series/sensor data, AWS SageMaker (model deployment), data-quality diagnostics.

---

## 5. Internal LLM Initiatives

**What:**
- Helped define internal guidelines for company-wide ChatGPT usage.
- Built a chatbot POC over an agri-robotics client's product manuals.
- Led a second LLM project (as lead engineer) doing support-ticket matching — took the POC to completion and handed it to a junior colleague to continue.
- Later prototyped a RAG (Retrieval-Augmented Generation) chatbot over a client's internal knowledge base, built in Databricks, integrating four different data sources: structured tables, PDFs, and scraped internal documentation.

**Why:** Early, practical exploration of where LLMs could reduce manual support/lookup work for clients, done responsibly (guidelines first) before building product POCs.

**Tools:** RAG, Databricks, multi-source data integration (structured + unstructured).

---

## 6. Infrastructure

**What:** Migrated data pipelines from GitLab CI/CD to GitHub Actions.

---

## Career Progression

Individual contributor on R&D/modeling work → Lead Project Engineer on a new client account (May 2023) → Lead Engineer on a second LLM initiative (Dec 2023) → Lead Engineer on a second transportation-client engagement (Feb 2024). Consistent pattern of being handed lead ownership on new engagements and successful client hand-offs at project close.

---

## Tech Stack Summary

**ML/Stats:** Bayesian optimization (GP, Sparse GP, TPE, Bayesian GLM), classical data-science libraries, time-series filtering (Kalman filter etc.), Fourier transforms. Rocket, Sktime
**LLM/GenAI:** RAG, Databricks, multi-source retrieval (structured + PDF + scraped docs), internal LLM usage guidelines.
**MLOps/Infra:** AWS SageMaker (deployment), DVC, GitLab CI/CD, GitHub Actions, Docker/packaging.
**Domains:** Agriculture/robotics (dairy automation), food-processing equipment, transportation/EV fleets.