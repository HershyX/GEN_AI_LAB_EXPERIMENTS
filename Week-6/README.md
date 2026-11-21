# Week 6: Golden Question Set & Baseline Evaluation (Release v0.1)

## 1. Overview & Objective
This experiment establishes a formal evaluation pipeline for an LLM feature. Students define a curated **Golden Question Set** of 30 benchmark questions covering core Generative AI concepts, execute an automated evaluation against a local `llama3.2` model via Ollama, score generated responses using keyword matching, and establish a baseline release labeled **Release v0.1**.

---

## 2. What the Experiment Does
1. **Loads Golden Set (`golden_set.json`):** Reads 30 standardized evaluation questions (Q001 to Q030) containing expected answers, key concepts/keywords, and verified reference sources.
2. **Executes Automated Batch Inference:** Queries the local Ollama instance running `llama3.2` with temperature set to 0 for deterministic output.
3. **Performs Keyword Coverage Scoring:** Computes the percentage of target keywords present in the generated answer:
   $$\text{Score} = \left( \frac{\text{Matched Keywords}}{\text{Total Expected Keywords}} \right) \times 100$$
4. **Assigns Release Status:** Questions scoring $\ge 60\%$ are marked `PASS`; otherwise marked `REVIEW`.
5. **Exports Artifacts:** Persists all individual query results to `baseline_results.csv` and outputs summary metrics.

---

## 3. Directory Structure
```text
Week-6/
├── golden_set.json       # 30 benchmark questions with expected answers & keywords
├── main.py               # Evaluation engine, scoring, CSV exporter
├── baseline_results.csv  # Generated CSV benchmark report
├── requirements.txt      # Python dependencies
└── README.md             # Experiment documentation
```

---

## 4. Setup & Configuration

### Prerequisites
* Ollama running locally with `llama3.2`:
  ```powershell
  ollama serve
  ollama pull llama3.2
  ```

### Dependencies (`requirements.txt`)
* `requests`

---

## 5. How to Run

```powershell
cd "c:\KNOX\KLH\2nd Year\TRIMESTER-2\GEN AI\LAB_EXP\Week-6"
..\Myenv\Scripts\python.exe main.py
```

---

## 6. Experimental Results (Release v0.1 Summary)

```text
======================================
BASELINE EVALUATION SUMMARY
======================================
Release Version:            v0.1
Model:                      llama3.2
Total Questions:            30
Successful Responses:       30 (100%)
Passed Questions (>=60%):   15
Questions for Review:       15
Failed Requests:            0
Average Keyword Coverage:   55.28 %
Results Saved To:           baseline_results.csv
======================================
```

### Sample CSV Row (`baseline_results.csv`):
| Field | Value |
| :--- | :--- |
| **Question_ID** | Q001 |
| **Question** | What is tokenization in large language models? |
| **Expected_Answer** | Tokenization is the process of splitting input text into smaller units called tokens. |
| **LLM_Answer** | Tokenization is a crucial step in the processing of large language models. It involves breaking down input text into individual tokens... |
| **Matched_Keywords** | text, tokens |
| **Missing_Keywords** | splitting |
| **Score** | 66.67% |
| **Status** | PASS |
| **Release** | v0.1 |

---

## 7. Key Concepts Covered
* **Golden Datasets:** Constructing high-quality ground-truth evaluation datasets for LLM benchmarking.
* **Deterministic Evaluation:** Setting `temperature=0` to evaluate models consistently without random sampling variation.
* **Keyword-Based Evals:** Quantifying factual accuracy and concept coverage through keyword verification.
* **Release Baselines:** Versioning LLM prompt/model evaluations to track regressions across iterations.
