# TrapBuster — Formal Project Proposal

**Course:** Web & Data Science, Politecnico di Milano (2026/27)  
**Team:** Two students  
**Target presentation:** 17 December 2026  
**Document status:** Proposal v1.0 — subject to instructor feedback  
**Related document:** [Software Requirements Specification](./requirements.md)

## 1. Project summary

TrapBuster is a research prototype for **explainable fake-review risk analysis**. It combines a public-facing **Review Analyzer** with a **Research Dashboard** to study whether review text, historical reviewer behavior, and reviewer–item relationships provide complementary evidence of suspicious online reviews.

The application will display a **suspicion score from 0 to 100**, supported by feature-based explanations and explicit limitations. This score must **not** be presented as a verified probability that a review is fraudulent. Where only review text is available, the system will use a text-only model; behavior- or graph-based analysis requires legitimate access to historical context.

The scientific contribution is a controlled, reproducible comparison of **text-only**, **behavior-only**, and **late-fusion** approaches under realistic evaluation constraints and imperfect labels.

## 2. Problem and motivation

Online reviews affect consumer decisions, but deceptive reviews can be difficult to identify from wording alone. Text classifiers may learn superficial linguistic cues; reviewer history and connections to products or businesses may provide complementary signals. However, many research datasets use operational or proxy labels (for example, platform filtering decisions or reviewer-level classifications), which should not be treated as independently proven fraud.

A second challenge is evaluation bias. Random review splits may place reviews from the same reviewer in both training and test sets, or allow features computed with future information. Such leakage can exaggerate reported performance.

TrapBuster therefore focuses on **generalization, transparency, and evaluation validity**, rather than promising perfect fake-review detection.

## 3. Research question and hypotheses

**Main research question**

> To what extent do behavioral and relational signals improve detection of suspicious online reviews compared with text-only models, when evaluation controls for reviewer overlap and acknowledges noisy proxy labels?

**Subquestions**

1. How does a text-only model compare with a behavior-only model on the same labeled subset and evaluation split?
2. Does late fusion improve PR-AUC and fake-class F1 over either specialist?
3. How do results change under reviewer-disjoint evaluation and, if data coverage permits, chronological evaluation?
4. How sensitive are the methods to *simulated* changes in training labels, and which patterns explain false positives and false negatives?

**Hypotheses to test (not assumed results)**

- H1: Complementary behavioral history can improve discrimination when context is available and labels are sufficiently informative.
- H2: Performance estimated using random review splits may be more optimistic than performance on unseen reviewers.
- H3: A modular hybrid may provide clearer feature-level explanations than deeply integrated approaches, though it may not achieve higher predictive performance.

## 4. Proposed application

### 4.1 Review Analyzer

A user pastes review text, optionally adds a rating and date, and receives:
- A 0–100 **suspicion score** calculated from a trained model output.
- The **analysis mode** used (text-only or context-enriched).
- Evidence-based explanations connected to the actual model/features.
- A warning that the result reflects patterns learned from imperfect training labels, not confirmed fraud.
- Explicit information about unavailable historical and relational data.

Example reviews from the permitted research dataset will demonstrate context-enriched analysis. Support for arbitrary Google Maps/Amazon review URLs is an **optional integration**, conditional on documented authorization and availability; it is **not required for the MVP**.

### 4.2 Research Dashboard

The dashboard will expose dataset characteristics, model comparison results, confusion matrices, precision–recall curves, error cases, experimental protocol, and limitations. Reviewer–item network visualizations and sensitivity analyses are extensions when feasible.

## 5. Data and labeling strategy

**Dataset selection is pending a documented audit.** Candidates include YelpChi/YelpNYC/YelpZip with review text and metadata, and research-grade Amazon review datasets with reviewer-level labels. Selection depends on lawful access, fields available, label provenance, missingness, sample size, and suitability for user-grouped evaluation.

Before modeling, the team will record:
- Origin, license/access restrictions, schema, size, class balance, and missing fields.
- Whether labels refer to reviews or reviewers and how they were produced.
- Whether review text, timestamps, reviewer IDs, and product/business IDs coexist.
- Whether labels may be proxies, generated labels, or partially synthetic.

**Critical caution:** Yelp filtered/recommended status is a platform decision, not verified fraudulent/genuine ground truth. Reviewer-level annotations must not silently be described as verified review-level fraud. Unlabeled Amazon review corpora cannot be used to report true fraud prevalence.

## 6. Research and model methodology

The minimum controlled experiment compares:

| Model | Inputs | Candidate approach | Research purpose |
|---|---|---|---|
| M1 — Text-only | Review text | TF-IDF + Logistic Regression baseline; richer embeddings if feasible | Establish linguistic baseline |
| M2 — Behavior-only | Reviewer/item history and time-dependent features | LightGBM or comparable tabular model | Isolate behavioral evidence |
| M3 — Late fusion | Text-model output + available behavioral features | Stacking/meta-classifier trained without target leakage | Test complementary signals |

**M4 — Deep graph–text hybrid (e.g., FraudSquad-style architecture)** is a *stretch comparison*, not an MVP acceptance condition. Likewise, fine-tuning DeBERTa, zero-shot LLMs, adversarial camouflage generation, and auditing 2022–2023 Amazon data are exploratory extensions proposed in the original overview, subject to feasibility.

**Experimental controls**

1. Use consistent train/validation/test partitions for directly comparable models.
2. Prefer reviewer-grouped splits for the primary comparison.
3. Fit text vectorizers, encoders requiring fitting, imputers, feature normalizers, and models using training data only.
4. Build reviewer features from information available at the prediction cutoff; ensure graph statistics do not use prohibited test edges or future events.
5. Train stacking on out-of-fold predictions or a properly separated validation set; do not train a meta-model on in-sample specialist predictions.
6. Report PR-AUC, precision, recall, F1 by class, confusion matrices, class prevalence, and chosen thresholds.
7. Include error analysis and limitations; compare results across datasets only when protocols and label meanings align.
8. If testing label noise, explicitly distinguish *simulated label corruption* from actual unknown label error rates.

**No minimum model score is a success criterion.** Negative or inconclusive findings remain valid if experiments are reproducible and interpreted correctly.

## 7. Deliverables

1. Working web prototype with Review Analyzer and Research Dashboard.
2. Public GitHub repository with source code, reproducible experiments, tests, model/version information, and setup instructions.
3. Technical research report (target **6–8 pages**, subject to course confirmation).
4. Error analysis and documentation of AI-assisted work.
5. Final presentation and live demonstration on **17 December 2026**.

## 8. Milestones and progress reviews

| Dates (2026) | Planned evidence |
|---|---|
| 25–26 November | Requirements, dataset audit, evaluation design, initial baseline |
| 2–3 December | Text and behavior specialists, initial same-split comparison |
| 9–10 December | Late fusion, error analysis, dashboard demonstration |
| 16–17 December | Verified results, report, repository documentation, live presentation |

**Recurring checkpoints:** Wednesday **10:00** and Thursday **12:00** (local Milan time). Milestones are targets, not claims of completed work.

## 9. Risks and mitigations

| Risk | Mitigation |
|---|---|
| Proxy or inconsistent labels | Audit provenance; qualify claims; report dataset-label metrics only |
| Missing reviewer history for a pasted review | Fall back to text-only; disclose missing features |
| Leakage through reviewer overlap or future activity | Grouped splits, time-aware feature creation, validation tests |
| Different datasets cannot be combined directly | Separate benchmark results; do not compare incompatible protocols |
| Limited time and compute | Prioritize M1–M3; treat GNN/LLM experiments as stretch goals |
| API/scraping restrictions | Do not depend on live external retrieval for the demo |
| Unreliable risk calibration | Label result as suspicion score; assess calibration before probability claims |

## 10. Preliminary references

- Rayana, S. & Akoglu, L. (2015). *Collective Opinion Spam Detection: Bridging Review Networks and Metadata*. KDD. https://doi.org/10.1145/2783258.2783370
- Dou, Y. et al. (2020). *Enhancing Graph Neural Network-based Fraud Detectors against Camouflaged Fraudsters*. https://arxiv.org/abs/2008.08692
- *Detecting LLM-Generated Spam Reviews by Integrating Language Model Embeddings and Graph Neural Network* (FraudSquad; preprint, authors/version to be confirmed in the literature inventory). https://arxiv.org/abs/2510.01801
- Hou, Y. et al. (2024). *Bridging Language and Items for Retrieval and Recommendation* (Amazon Reviews 2023). https://arxiv.org/abs/2403.03952

The literature inventory, dataset selection, and specific model configurations will be updated with verified evidence during the data-audit stage.
