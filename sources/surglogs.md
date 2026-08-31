# Surglogs — AI Engineering Work Summary (Mar 2025 – Aug 2026)

Reference notes for talking about my work at Surglogs (AI Engineer, contract). Surglogs builds compliance/accreditation software for outpatient surgery centers. This covers everything I built beyond the core ~500-label hierarchical document classification system (that's a separate, deeper ML project — ask if you want that one too).

---

## 1. Policy → Accreditation Requirement Mapping Engine

**What:** A hybrid classification service that reads a healthcare policy's raw text and maps it to the specific accreditation requirement IDs it satisfies, across five standards: TJC, AAAHC, ACHC, CMS, and QUAD-A.

**Why:** Surgery centers must prove their internal policies satisfy hundreds of accreditation requirements. Doing this manually per-standard doesn't scale — this automates the mapping so compliance staff get a ranked, sourced, confidence-scored answer instead of reading requirement checklists by hand.

**How it works — 3 parallel branches merged per request:**
- **LLM branch:** Gemini with structured-output extraction (label/reason/relevance), often assisted by per-standard MLflow category/chapter-mapper models.
- **Semantic branch:** dual embeddings (legacy 1536d, current 3072d) → vector similarity search → frequency-normalized score aggregation, thresholded to filter weak matches.
- **Exact-match branch:** pre-trained keyword/requirement rule mappings.

**Interface:** internal gRPC service exposing endpoints to classify a policy, generate/store its embedding, and LLM-clean policy text before training. Response includes requirement ID, reason, relevance tier, confidence tier, and source attribution.

**Standard-specific predictors:** TJC (LLM+semantic+exact), AAAHC (XGBoost category classifier + LLM, later a full multi-branch version), CMS (semantic pre-labeling → MLflow chapter mapper), ACHC (LLM-based), TJC Hospital 2026, QUAD-A (multi-variant).

**Storage:** Postgres + pgvector, with a fix I shipped to prevent duplicate embedding-cache entries via a unique constraint + conflict-safe inserts.

**Tools:** Gemini (structured outputs), pgvector, MLflow, XGBoost, gRPC/Protobuf, Postgres.

---

## 2. Enterprise Semantic Search

**What:** Hybrid full-text + semantic search over policy content for the platform.

**Why:** Users need to find relevant policies fast across full-text keyword matches AND semantically similar content the keywords miss.

**Key changes I shipped:**
- Migrated to a v2 index architecture: parallel indices, versioned aliases, upgraded embedding model, recursive chunking.
- Dropped LLM text preprocessing for v2 (simplification/cost win).
- Fixed vector quality by truncating + L2-normalizing embeddings (both indexed and query side) — a real bug fix, not just a config change.
- Made v2 the default and retired new v1 indexing.

**How it works:**
- Full-text and query-embedding generation run concurrently.
- Full-text: fuzzy matching, multi-match, wildcards, name-prefix matching.
- Semantic: nested KNN search against summary/content/note embeddings.
- Both result sets min-max normalized, then combined with weighted scoring.
- Indexing: content split into large overlapping chunks, embedded, stored as nested HNSW cosine vectors.

**Tools:** OpenSearch (HNSW/KNN), Gemini embeddings, chunking pipeline.

---

## 3. Log Classification & OCR / Log Creation Pipelines

**What:** Two related pipelines —
1. **Log classification:** pure-LLM classification of operational logs — no semantic embedding step needed since log formats are highly similar/fuzzy-matchable across centers (few-shot over a small set of examples). Also built enterprise-log severity scoring served via gRPC through MLflow.
2. **Log creation:** OCR + full backend pipeline (async task workers) that generates logs from source documents. Included an agentic self-review step (retrieval of few-shot examples, RAG-style) to catch errors before output. The hardest part was building a good evaluation dataset — no existing gold-standard set.

**Why:** Reduce manual log entry/transcription and give consistent severity triage across enterprise customers.

**Tools:** Gemini, Celery, OCR, MLflow (serving via gRPC), RAG-style few-shot retrieval.

---

## 4. Findings / Mock Survey Model

**What:** OCR reading + simpler LLM-based mapping for a "findings" (mock survey) feature.
**Tools:** OCR, LLM-based mapping (lighter-weight than the policy engine).

---

## 5. Prompt Engineering & Eval Infrastructure

**What:** Set up MLflow-logged, versioned, registered prompt experiments, with automated eval via Ragas and DeepEval.
**Why:** Make prompt iteration measurable and reproducible instead of ad hoc — track what changed, what got better/worse, and roll back if needed.
**Tools:** MLflow (experiment tracking/registry/versioning), Ragas, DeepEval.

---

## 6. Data Quality Fixes

**What:** Used Cleanlab to rank likely-mislabeled training data; added KNN and SVC to broaden the probability ensemble; used Local Outlier Factor (multiple distance metrics) to flag unusual policies for human review (not auto-relabeling).
**Why:** Training data quality was a bigger lever on model performance than model architecture changes.
**Tools:** Cleanlab, KNN/SVC ensembles, Local Outlier Factor.

---

## 7. Support LLM Agents

**What:** Two agent tools for internal/support use:
- Log comparison agent — diffs how two centers' logs differ.
- Policy gap report agent — generates a report of accreditation gaps from policy analysis.

---

## Experiments & R&D Highlights (talking points with numbers)

- **Category imbalance:** tuned focal-loss XGBoost for the classification models — graduated from notebook experiments into production training scripts.
- **Semantic requirement suggestions vs. existing model:** starting from a lower-recall baseline, adding a semantic-similarity branch nearly doubled recall on its own (at a precision cost); combining strategies and filtering brought overall F1 up meaningfully while improving precision *and* recall together.
- **TF-IDF → embeddings migration:** replaced TF-IDF classifiers (AAAHC, TJC) with embedding-based XGBoost + LLM fallback, then upgraded the embedding model version. The embedding-based AAAHC classifier reached ~0.75 F1 in evaluation (precision and recall both in the low-to-mid 70s) over several hundred policies.
- **Alternative LLM provider evaluation:** built (not fully finished) a framework comparing Claude Sonnet/Opus vs. Gemini vs. the hybrid pipeline on quality, latency, token cost.
- **Coverage expansion:** added ACHC and TJC Hospital 2026 predictors; migrated most LLM-backed models to a newer, faster Gemini tier.
- **MLOps maturation:** moved from notebooks → automated candidate evaluation/promotion (DVC) → MLflow model registration → full CI-based training pipelines.

---

## Tech Stack Summary

**Models/APIs:** Gemini (multiple generations), Gemini embeddings (two versions), Claude (Sonnet/Opus, evaluated), XGBoost (focal loss), MLflow-registered chapter/category mapper models.
**Infra/data:** PostgreSQL + pgvector, OpenSearch (HNSW/KNN), Celery, gRPC/Protobuf, MLflow (tracking/registry), DVC, CI-based training pipelines.
**Eval/quality:** Ragas, DeepEval, Cleanlab, Local Outlier Factor, custom benchmark utilities.
**Domain:** Multi-standard healthcare accreditation compliance (TJC, AAAHC, ACHC, CMS, QUAD-A).