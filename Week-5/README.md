# Week 5: Token Economics & Rate-Limit Budgeting

## 1. Overview & Objective
Before building an LLM feature, engineering teams must evaluate cost feasibility and capacity limits. This experiment builds a pre-build budget planner that ingests traffic assumptions, computes token volumes and costs, checks Requests Per Minute (RPM) and Tokens Per Minute (TPM) limits with safety margins, and outputs an automated Go / Revise recommendation.

---

## 2. What the Experiment Does
1. **Models Traffic Assumptions:** Loads variables from `.env` including requests/day, average input/output tokens per request, and peak RPM.
2. **Calculates Daily & Monthly Spend:** Computes daily and 30-day hosted API costs using configured input and output rates per 1,000,000 tokens.
3. **Applies Safety Margins:** Adds a safety buffer (e.g., 20%) to peak traffic and cost forecasts to absorb traffic spikes, long prompts, and retry overhead.
4. **Evaluates Capacity & Budget Checks:**
   * RPM Capacity: `peak_rpm * margin <= rpm_limit`
   * TPM Capacity: `peak_tpm * margin <= tpm_limit`
   * Daily Budget: `daily_cost * margin <= daily_budget`
   * Monthly Budget: `monthly_cost * margin <= monthly_budget`
5. **Provides Recommendation:** Reports `WITHIN configured planning limits` if all checks pass, or flags specific areas requiring redesign.

---

## 3. Directory Structure
```text
Week-5/
├── .env                  # Planning assumptions, rate limits, budgets
├── budget.py             # FeatureBudget dataclass, calculator, and report generator
├── test_budget.py        # Automated unit tests for calculations and limits
├── requirements.txt      # Python dependencies
└── README.md             # Experiment documentation
```

---

## 4. Setup & Configuration

### Planning Assumptions (`.env`)
```env
# All prices are USD per 1,000,000 tokens
INPUT_PRICE_PER_MILLION=0.30
OUTPUT_PRICE_PER_MILLION=1.20
REQUESTS_PER_DAY=2000
AVG_INPUT_TOKENS=1200
AVG_OUTPUT_TOKENS=300
PEAK_REQUESTS_PER_MINUTE=30
RPM_LIMIT=60
TPM_LIMIT=100000
MONTHLY_BUDGET_USD=50
DAILY_BUDGET_USD=2
SAFETY_MARGIN_PERCENT=20
```

### Dependencies (`requirements.txt`)
* `python-dotenv`

---

## 5. How to Run & Test

### Run Unit Tests
```powershell
cd "c:\KNOX\KLH\2nd Year\TRIMESTER-2\GEN AI\LAB_EXP\Week-5"
..\Myenv\Scripts\python.exe -m unittest test_budget.py
```
*Expected Output:*
```text
....
----------------------------------------------------------------------
Ran 4 tests in 0.000s

OK
```

### Run Budget Planner
```powershell
..\Myenv\Scripts\python.exe budget.py
```

---

## 6. Sample Execution Output

```text
TOKEN ECONOMICS REPORT
-----------------------------------
Input tokens/day:  2,400,000
Output tokens/day: 600,000
Total tokens/day:  3,000,000
Estimated daily cost:   $1.4400
Estimated monthly cost: $43.20
Peak RPM with margin: 36.0
Peak TPM with margin: 54000

CHECKS
RPM capacity: PASS
TPM capacity: PASS
Daily budget: PASS
Monthly budget: REVIEW

Recommendation: REVISE assumptions, limits, or design before build.
```

*Analysis:* While raw monthly cost is \$43.20, applying the 20% safety margin brings the projected monthly cost to \$51.84, which exceeds the configured \$50.00 monthly budget. The tool correctly flags this for review prior to writing code.

---

## 7. Key Concepts Covered
* **Token Budgeting:** Estimating tokens before launch to avoid unexpected cloud expenditure.
* **Rate Limits (RPM & TPM):** Differentiating financial affordability from provider throughput capacity.
* **Safety Margin Modeling:** Planning for traffic surges and variance between p50 and p95 request sizes.
