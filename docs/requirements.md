# TrapBuster — Software Requirements Specification (SRS)

**Version:** 1.0  
**Status:** Baseline for architecture design; priorities and acceptance tests defined  
**Target delivery:** 17 December 2026  
**Team:** Two students  
**Project:** Web & Data Science, Politecnico di Milano  
**Related document:** [Formal Project Proposal](./proposal.md)

## 1. Purpose, scope, and principles

TrapBuster is a research web application with two experiences: **Review Analyzer** for individual review assessment and **Research Dashboard** for model comparison and reproducible experimental evidence.

**Output contract:** The public-facing result is a **0–100 suspicion score with explanations**, **not a definitive fake/genuine label** and **not automatically a calibrated real-world fraud probability**.

**Design principles:**
- Do not invent missing reviewer histories or network signals.
- Clearly identify the input data, model, version, and operating mode.
- Attribute explanations only to features or outputs actually used.
- Keep experimental evidence distinct from illustrative demo content.
- Treat benchmark and reviewer-derived labels as potentially imperfect proxies.

Requirements use **Must / Should / Could** priority. Every Must requirement must pass its acceptance criteria for the final MVP.

## 2. Actors and primary use cases

| Actor | Use case | Expected result |
|---|---|---|
| Online shopper / visitor | Paste an individual review | Risk score and grounded explanation |
| Researcher / student | Inspect labeled samples and model outputs | Dataset context, patterns, and reproducible comparisons |
| Instructor / evaluator | Demonstrate or inspect experiment evidence | Traceable methods, metrics, and limits |

No authentication or persistent individual user profile is required.

## 3. Functional requirements

### 3.1 Review Analyzer

| ID | Priority | Requirement |
|---|---|---|
| FR-01 | Must | Accept pasted nonempty review text. |
| FR-02 | Must | Accept optional review rating, date, and contextual metadata where provided. |
| FR-03 | Must | Validate submissions and reject invalid or oversized inputs. |
| FR-04 | Must | Generate a 0–100 suspicion score from an applicable trained model, including model ID/version. |
| FR-05 | Must | Provide understandable explanations grounded in model features or computed attribution. |
| FR-06 | Must | Identify analysis mode (text-only or context-enriched). |
| FR-07 | Must | Disclose unavailable history/features and uncertainty of training labels. |
| FR-08 | Must | Load example reviews from an available, permitted research dataset. |
| FR-09 | Must | For samples with eligible historical context and trained models, show text, behavior, and late-fusion outputs. |
| FR-10 | Could | Accept a Google/Amazon review URL *only* through compliant, supported content access. |

### 3.2 Research Dashboard

| ID | Priority | Requirement |
|---|---|---|
| FR-11 | Must | Show dataset size, schema summary, label distribution and missingness. |
| FR-12 | Must | Compare text-only, behavior-only and late-fusion models on aligned test partitions. |
| FR-13 | Must | Show precision, recall, class-wise F1 and PR-AUC. |
| FR-14 | Must | Show confusion matrices and precision–recall curves. |
| FR-15 | Must | Show representative false positives/negatives **relative to dataset proxy labels**. |
| FR-16 | Should | Filter and inspect individual research samples by available metadata. |
| FR-17 | Should | Visualize reviewer–item networks or suspicious behavior patterns when supported by data. |
| FR-18 | Must | Display split methodology, data source, label provenance and model/experiment IDs. |
| FR-19 | Should | Present reviewer-disjoint vs random or time-based evaluation and/or label-noise sensitivity, when data permits. |
| FR-20 | Could | Export experiment summaries in CSV format. |

## 4. Acceptance criteria

Each criterion includes an observable test. All core checks must operate with **actual model outputs**, not fixed demonstration values.

| ID | Related requirements | Acceptance test |
|---|---|---|
| AC-01 | FR-01, FR-04 | Submit five valid review texts; each returns a model-generated score and model identifier. |
| AC-02 | FR-03 | Empty, whitespace-only and over-limit submissions show validation feedback without inference or app crash. Document the chosen input length limit before testing. |
| AC-03 | FR-04, FR-07 | All displayed scores are numeric in [0,100], computed consistently from model output, and accompanied by the phrase “suspicion score” and a proxy-label warning. |
| AC-04 | FR-05 | Audit explanations for five predictions; every attributed factor maps to a computed feature/attribution. Do not claim model reasoning from heuristic text alone. |
| AC-05 | FR-06, FR-07 | Submit text without reviewer history: only text-based scoring is available; behavioral/late-fusion outputs are marked unavailable. |
| AC-06 | FR-08, FR-09 | Select at least three eligible research samples; verify specialist and hybrid outputs against direct predictions from their saved model versions. |
| AC-07 | FR-04 | Repeat inference for the same input/model/preprocessing; results agree within a documented numerical tolerance. |
| AC-08 | FR-11 | Dataset counts, class distribution and missingness match independently computed dataset summaries. |
| AC-09 | FR-12, FR-18 | Report M1/M2/M3 results; all directly compared runs share documented dataset subset and test identifiers. |
| AC-10 | FR-13, FR-14 | Recalculate confusion matrix, PR curve, class-wise precision/recall/F1 and PR-AUC from stored predictions; values match dashboard metrics (within numerical tolerance). |
| AC-11 | FR-15 | Show at least five false positives and five false negatives *when present*, labeled with proxy-label semantics; avoid exposing sensitive reviewer identifiers. |
| AC-12 | FR-18 | Every experiment view identifies dataset/version, label origin, partition protocol, model/version, and run identifier/date. |
| AC-13 | FR-02, FR-06 | Missing optional metadata does not prevent text analysis or cause invented contextual feature values. |
| AC-14 | FR-07 | Every prediction view contains the limitation that model-estimated risk is not proof of fraud. |

## 5. Non-functional requirements

| ID | Quality attribute | Requirement and verification |
|---|---|---|
| NFR-01 | Usability | First-time participant completes text analysis without instructions; manually test the core flow. |
| NFR-02 | Performance | Target **under 5 s** for text-only inference *after model loading*, measured on the final environment and recorded; if unmet, document latency and constraints rather than misreporting compliance. |
| NFR-03 | Reliability | Invalid inputs and unavailable resources produce controlled errors and do not crash the application. |
| NFR-04 | Reproducibility | Record seeds, dependency versions, dataset identifiers, configuration, preprocessing and output artifacts. |
| NFR-05 | Maintainability | Data transformations, inference and evaluation are independently testable with automated tests. |
| NFR-06 | Transparency | Every public score explains the label limitations, analysis mode, missing signals and model version. |
| NFR-07 | Privacy | Minimize personal data and avoid public disclosure of raw reviewer identifiers or private/restricted review content. |
| NFR-08 | Portability | A fresh environment can install dependencies, run tests and launch the application using the README. |
| NFR-09 | Accessibility | Visual risk information is accompanied by labels/text; never rely on color alone. |

## 6. Research requirements and controls

| ID | Requirement | Evidence |
|---|---|---|
| RR-01 | Audit dataset provenance, access rights, schema, class balance and label source before selecting a benchmark. | Dataset card and EDA summary |
| RR-02 | Explicitly separate proxy labels, reviewer-level classifications and verified review-level labels. | Label glossary and report limitations |
| RR-03 | Use reviewer-disjoint test splits for primary comparison; prohibit future leakage in temporal features. | Split-generation tests and train/test ID audit |
| RR-04 | Compare M1 text, M2 behavior and M3 late fusion using aligned partitions and out-of-fold meta-features or held-out meta-training data. | Configurations, fold IDs and experiment logs |
| RR-05 | Report PR-AUC, precision, recall, class-wise F1, class prevalence, confusion matrix and threshold selection. | Saved predictions + dashboard |
| RR-06 | Examine disagreements, false positives and false negatives without presuming proxy labels are infallible. | Error-analysis document |
| RR-07 | Discuss domain shift, unavailable reviewer context and generalization limits. | Technical report |
| RR-08 | Ensure replication with saved data preparation, parameters, dependencies and seeds. | Public repository documentation |

**Research integrity:** A higher hybrid F1 is a hypothesis, *not* an acceptance criterion. No metric threshold is promised before experiments.

## 7. Required data and model availability

| Field | Text-only | Behavior-only | Late fusion / relational |
|---|---|---|---|
| Review text | Required | Not required | Required for text component |
| Rating | Optional | Useful | Useful |
| Review timestamp | Optional | Needed for time features | Needed if time features used |
| Reviewer ID | Not required | Required for reviewer history | Required when behavior included |
| Item/business ID | Not required | Required for item statistics | Required when item context included |
| Historical reviews | Not required | Required | Required |

If historical context is unavailable, use **text-only mode**. Missing contextual information must not be synthesized.

## 8. Out of scope

- User registration, authentication, personal accounts.
- Unrestricted Google Maps/Amazon URL scraping or monitoring.
- Real-time retrieval of arbitrary reviewer histories from commercial websites.
- Definitive fraud verdicts or legal/individual accusations.
- Training new large foundation models from scratch.
- Full deep graph–text model replication as a required deliverable.

## 9. Deliverables and Definition of Done

**Deliverables:** (1) web prototype; (2) public code/experiment repository; (3) technical report targeting 6–8 pages subject to course requirements; (4) error & AI-use documentation; (5) final presentation and live demo on **17 December 2026**.

**Definition of Done**
- All **Must** functional requirements have passing documented acceptance tests.
- Dashboard results are generated from reproducible, recorded experiments.
- At least the three specialist/fusion configurations M1–M3 have results on aligned evaluation partitions.
- The Review Analyzer presents real outputs, clear explanations, model provenance and missing-data behavior.
- The live demo can run without unauthorized external access.
- The report, README and repository accurately reflect implemented behavior and observed findings.

## 10. Traceability and change management

This SRS is the baseline for the architecture stage. Design decisions must reference requirement IDs. Changes to Must-level scope, dataset choice or score semantics should update this document with a version note and rationale.

**Current state:** Specification agreed; implementation status and model scores must be established from verifiable repository artifacts rather than inferred from this document.
