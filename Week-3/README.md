# Week 3: LLM Observability & Monitoring with MongoDB Atlas

## 1. Overview & Objective
This experiment extends the provider-agnostic LLM client from Week 2 by adding comprehensive observability and logging. Every LLM request—whether routed to Google Gemini or local Ollama—is measured for latency, token consumption, and estimated financial cost, and recorded into a persistent MongoDB Atlas cloud database.

---

## 2. What the Experiment Does
1. **Captures Token Usage:** Extracts input prompt tokens and generated output tokens from provider metadata (`usage_metadata` for Gemini, `prompt_eval_count` / `eval_count` for Ollama).
2. **Measures Latency:** Calculates high-precision end-to-end request duration using `time.perf_counter()`.
3. **Calculates Estimated Cost:** Uses configurable per-million token pricing rates in `.env` to compute estimated USD cost per call.
4. **Persistent Logging to MongoDB Atlas:** Inserts structured usage documents into MongoDB Atlas (`GEN_AI_TOKENS_DB` database, `API_Usages` collection).
5. **Resilient Retry Handling:** Implements exponential backoff retry for transient network and rate-limit errors (HTTP 429, 503, 504), logging failed request attempts if all retries are exhausted.

---

## 3. Directory Structure
```text
Week-3/
├── .env                  # MongoDB Atlas URI, Gemini API key, pricing parameters
├── .gitignore            # Excludes credentials and cache
├── llm_client.py         # LLMClient with latency, token tracking, retry logic
├── usage_logger.py       # UsageLogger connecting to MongoDB Atlas with write retry
├── main.py               # Interactive CLI driver with resource cleanup
├── test_client.py        # Automated unit tests using FakeLogger and FakeProvider
├── requirements.txt      # Python dependencies
└── README.md             # Experiment documentation
```

---

## 4. Setup & Configuration

### Prerequisites
* Active MongoDB Atlas cluster with Network Access whitelist configured (`0.0.0.0/0` or current IP)
* Database credentials configured in MongoDB Atlas Database Access

### Environment Configuration (`.env`)
```env
GEMINI_API_KEY=your_gemini_api_key
MONGODB_URI=mongodb+srv://harshitsajjanapu_db_user:tejaharshit@cluster0.o5mfj6h.mongodb.net/
MONGODB_DB_NAME=GEN_AI_TOKENS_DB
MONGODB_COLLECTION_NAME=API_Usages
GEMINI_INPUT_PRICE_PER_MILLION=0
GEMINI_OUTPUT_PRICE_PER_MILLION=0
GEMINI_MODEL=gemini-3.5-flash-lite
```

### Dependencies (`requirements.txt`)
* `python-dotenv`
* `google-genai`
* `requests`
* `pymongo`
* `pytest`
* `dnspython`

---

## 5. How to Run & Test

### Run Unit Tests
```powershell
cd "c:\KNOX\KLH\2nd Year\TRIMESTER-2\GEN AI\LAB_EXP\Week-3"
..\Myenv\Scripts\python.exe -m unittest test_client.py
```
*Expected Output:*
```text
.
----------------------------------------------------------------------
Ran 1 test in 0.000s

OK
Latency: 0.0 ms
Input tokens: 5
Output tokens: 8
Estimated cost (USD): 0.0
```

### Run Live Application
```powershell
..\Myenv\Scripts\python.exe main.py
```

---

## 6. Live Execution & MongoDB Record

### Terminal Output:
```text
Choose provider (gemini/ollama): gemini
Enter your question: What is Generative AI?

Usage details saved to MongoDB.
Latency: 4069.62 ms
Input tokens: 7
Output tokens: 742
Estimated cost (USD): 0.0

LLM Response:
Generative AI is a type of artificial intelligence technology that can create new content...
```

### Document Recorded in MongoDB Atlas (`GEN_AI_TOKENS_DB.API_Usages`):
```json
{
  "_id": "ObjectId('...')",
  "provider": "Gemini",
  "model": "gemini-3.5-flash-lite",
  "input_tokens": 7,
  "output_tokens": 742,
  "total_tokens": 749,
  "latency_ms": 4069.62,
  "estimated_cost_usd": 0.0,
  "cost_type": "estimated_hosted_api_cost",
  "status": "success",
  "attempts": 1,
  "timestamp": "2026-11-13T16:41:19.645Z"
}
```

---

## 7. Key Concepts Covered
* **LLM Observability:** Instrumenting inference calls to monitor latency, token consumption, and failure rates.
* **Cost Governance:** Tracking hosted API spend before and during production use.
* **Cloud Database Integration:** Managing MongoDB connections via PyMongo, including socket timeouts and write retries.
