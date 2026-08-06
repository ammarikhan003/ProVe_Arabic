# ProVe-Arabic Archive — README

This archive contains the complete .ipynb/google colab files for **ProVe-Arabic**, a cross-lingual reference verification system for Arabic Wikidata statements. 

---

## Archive Contents

| Filename | Purpose / Focus | Primary Stage(s) |
|---|---|---|
| `ProVe_Arabic_Pipeline.ipynb` | End-to-end execution of the verification pipeline. | Stages A, B, C/D, E |
| `ProVe_Arabic_Evaluation.ipynb` | Evaluation harness, baselines, and cross-lingual experiments. | Stages C/D, E Evaluation |
| `ProVe_Arabic_DatasetConstruction.ipynb` | Dataset extraction, filtering, and annotation candidate sheet generation. | Data Preprocessing & Gold Construction |
| `ProVe_Arabic_Benchmarks.ipynb` | Empirical comparison of `pysbd`, `Stanza`, and `spaCy` for Arabic sentence boundary detection. | Stage B Benchmark & Analysis |

---

## File Descriptions

### 1. `ProVe_Arabic_Pipeline.ipynb`
This notebook implements the full end-to-end pipeline that verifies whether a referenced web page supports an Arabic Wikidata claim (triple: subject, property, object).

* **Stage A (Verbalisation):** Converts Wikidata triples into fluent Arabic sentences using a fine-tuned `mT5-small` model.
* **Stage B (Retrieval & Segmentation):** Fetches reference HTML pages, strips non-prose boilerplate, detects document language, normalises Arabic text with CAMeL Tools, and segments text into candidate evidence passages using `pysbd`.
* **Stage C/D (Cross-Lingual Entailment):** Scores each evidence passage against the Arabic claim using fine-tuned `mDeBERTa-v3` NLI to assign probabilities for `entailment`, `neutral`, and `contradiction`.
* **Stage E (Stance Aggregation):** Reduces per-passage entailment scores into a final document-level verdict (`SUPPORTS`, `REFUTES`, `NEI`) using a trained Random Forest classifier or heuristic rules.

---

### 2. `ProVe_Arabic_Evaluation.ipynb`
This notebook contains the evaluation framework, baseline comparisons, and experimental results reported in Chapter 5 of the report.

* **Dataset-Agnostic Scorer:** Provides uniform evaluation metrics (accuracy, confusion matrices, classification reports) across all benchmark datasets.
* **Passage-Level Baseline:** Evaluates Stage C/D entailment performance directly against human-annotated passages in the WTR benchmark.
* **Controlled Cross-Lingual Comparison:** Tests claim verification performance when comparing identical evidence passages against English claims versus Arabic claims.
* **Claim-Level Full Pipeline Run:** Evaluates the complete pipeline on archived WTR HTML documents, accounting for document retrieval and segmentation.
* **Stage E Aggregator Experiments:** Trains, cross-validates, and evaluates the 13-feature Random Forest stance aggregator model (`stage_e_rf.joblib`).

---

### 3. `ProVe_Arabic_DatasetConstruction.ipynb`
This notebook handles data extraction from Wikidata, text filtering, and dataset construction for evaluation sets and training target reviews.

* **SPARQL Data Extraction:** Queries the Wikidata SPARQL endpoint for statement triples accompanied by reference URLs.
* **Filtering & Quality Checks:** Applies filters to exclude database identifier properties, non-prose target types, dead links, and caps per-subject/reference redundancy.
* **Gold Candidate Sheet Generation:** Produces `arabic_gold_candidates.csv` containing candidate statement-reference pairs along with draft Arabic verbalisations for human annotation.
* **Training-Target Review Sheets:** Exports property-stratified CSV review sheets (`target_review_short.csv`) to review verbalisation targets and flag potential relation inversions.

---

### 4. `ProVe_Arabic_Benchmarks.ipynb`
This notebook provides a self-contained empirical comparison of sentence segmentation engines (`pysbd`, `Stanza`, and `spaCy`) to motivate the choice of `pysbd` in Stage B.

* **Segmenter Comparison:** Compares sentence-boundary detection stability across varied page types (clean prose, list-heavy structures, technical texts).
* **Fairness Normalisation:** Applies identical post-processing, spacing-tidying, deduplication, and minimum-length filtering across all three tools.
* **Standalone Analysis:** Runs outside the core pipeline execution path and installs benchmark-specific dependencies (`stanza`, `spacy`).

---

## Execution Requirements

* **Runtime:** Google Colab with a **T4 GPU** runtime.
* **Environment Dependencies:** `transformers==4.46.3`, `scikit-learn==1.8.0`, `pysbd`, `camel-tools`, `beautifulsoup4`, `lxml`, `requests`, `sacrebleu`, and `joblib`. *(Note: `ProVe_Arabic_Benchmarks.ipynb` additionally installs `stanza` and `spacy`).*
* **External Dataset:** Running evaluation experiments in `ProVe_Arabic_Evaluation.ipynb` requires `WTR.json` (automatically fetched from Figshare or supplied manually).