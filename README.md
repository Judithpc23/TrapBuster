# TrapBuster

**Explainable Fake Review Risk Analysis & Research Platform**

Academic project for **Web & Data Science — Politecnico di Milano (2026/27)**.  
**Final presentation:** 17 December 2026 · **Team:** 2 students

## Overview

TrapBuster explores whether combining **review text** with **reviewer behavior** and **reviewer–product/business relationships** can improve the identification of potentially suspicious online reviews.

The proposed web application has two parts:

- **Review Analyzer:** paste review text to obtain a **0–100 suspicion score**, grounded explanations, and clear notices about unavailable metadata.
- **Research Dashboard:** explore datasets, compare text-only, behavior-only and late-fusion models, inspect evaluation metrics, and analyze errors.

**Important:** A suspicion score is **not proof that a review is fake**. The research datasets may contain proxy labels rather than independently verified fraud; model outputs must be interpreted within those limitations.

## Research focus

> Do behavioral and relational signals improve detection beyond text-only methods when evaluation controls for reviewer overlap and acknowledges imperfect labels?

Planned model comparison:
1. **Text-only baseline** — initially TF-IDF + Logistic Regression.
2. **Behavior-only model** — tabular reviewer/item history features, e.g. LightGBM.
3. **Late fusion** — combine specialist outputs/features using a leakage-safe meta-classifier.

Graph neural networks, transformer fine-tuning, LLM-based comparison, and third-party URL integration are possible **extensions**, not established project features.

## Current status

**Requirements defined; architecture and implementation are being planned.** This repository is at its documentation stage. No runnable application, trained model, benchmark scores, or deployment are claimed by this README.

The formal proposal and SRS are maintained in the `docs` branch:

- [Formal project proposal](https://github.com/Judithpc23/TrapBuster/blob/docs/docs/proposal.md)
- [Software requirements specification (SRS v1.0)](https://github.com/Judithpc23/TrapBuster/blob/docs/docs/requirements.md)

## Planned evaluation

- Reviewer-disjoint training/test splits and controls against future-data leakage.
- Precision, recall, F1, **PR-AUC**, confusion matrices and precision–recall curves.
- Documented dataset and label provenance; reproducible experiments and error analysis.
- Comparison using aligned partitions; no unsupported cross-dataset score claims.

## Deliverables

Web prototype · public GitHub repository · technical report · error/AI-use documentation · final presentation and live demonstration.

**Progress reviews:** Wednesdays at 10:00 and Thursdays at 12:00 (Milan local time).

## Scope limitations

The MVP will not depend on scraping arbitrary Amazon or Google Maps URLs or obtaining complete reviewer histories from those services. Pasted review text will support text-only assessment; behavioral and hybrid assessment requires available, permitted historical data.

## License and datasets

A code license and dataset redistribution policy have **not yet been chosen**. Do not upload restricted, private or copyrighted datasets without confirming applicable permissions.
