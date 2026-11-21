# Week 2: Provider-Agnostic LLM Client

## 1. Overview & Objective
The goal of this experiment is to build a provider-agnostic LLM client using Object-Oriented Programming (OOP) and Python Abstract Base Classes (`abc.ABC`). The client provides a single, uniform interface that abstracts over both a hosted cloud API (Google Gemini) and a local offline model (Ollama running `llama3.2`).

---

## 2. What the Experiment Does
1. **Defines Common Interface (`LLMProvider`):** Specifies an abstract method `generate(prompt)` that all provider implementations must conform to.
2. **Implements Hosted Cloud Provider (`GeminiProvider`):** Connects to Google's GenAI API using API credentials to generate text remotely.
3. **Implements Local Provider (`OllamaProvider`):** Communicates with the local Ollama REST server (`http://localhost:11434/api/generate`) via HTTP POST requests, running inference completely offline on the local GPU (e.g. NVIDIA RTX 2050).
4. **Unified Client Wrapper (`LLMClient`):** Decouples application logic from specific LLM vendors, allowing callers to switch providers dynamically without changing client code.
5. **Automated Unit Testing (`test_client.py`):** Uses a `FakeProvider` mock class to test client logic independently without consuming API quota or requiring a running local model.

---

## 3. Directory Structure
```text
Week-2/
├── .env                  # API keys and model configurations
├── .gitignore            # Git exclusion rules
├── llm_client.py         # Abstract interface, GeminiProvider, OllamaProvider, and LLMClient
├── main.py               # Interactive CLI for testing Gemini and Ollama
├── test_client.py        # Unit tests with Mock/FakeProvider
├── requirements.txt      # Python dependencies
└── README.md             # Experiment documentation
```

---

## 4. Setup & Configuration

### Prerequisites
* Python virtual environment (`../Myenv`)
* Google Gemini API Key in `.env`
* Local Ollama installed with `llama3.2` model pulled:
  ```powershell
  ollama serve
  ollama pull llama3.2
  ```

### Dependencies (`requirements.txt`)
* `google-genai`
* `requests`
* `python-dotenv`
* `pytest`

---

## 5. How to Run & Test

### Run Unit Tests
```powershell
cd "c:\KNOX\KLH\2nd Year\TRIMESTER-2\GEN AI\LAB_EXP\Week-2"
..\Myenv\Scripts\python.exe -m unittest test_client.py
```
*Expected Result:*
```text
.
----------------------------------------------------------------------
Ran 1 test in 0.000s

OK
```

### Run Interactive Application
```powershell
..\Myenv\Scripts\python.exe main.py
```
* Prompt asks: `Choose provider (gemini/ollama):`
* Enter question: `What is AI?`

---

## 6. Sample Execution Output

### Running with Gemini:
```text
Choose provider (gemini/ollama): gemini
Enter your question: What is AI?

LLM Response:
AI stands for Artificial Intelligence. At its broadest level, AI refers to the development of computer systems and software capable of performing tasks that typically require human intelligence...
```

### Running with Local Ollama:
```text
Choose provider (gemini/ollama): ollama
Enter your question: What is AI?

LLM Response:
Artificial Intelligence (AI) refers to the development of computer systems that can perform tasks that would typically require human intelligence, such as learning, reasoning, perception, and problem-solving...
```

---

## 7. Key Concepts Covered
* **Abstraction & Polymorphism:** Creating provider-agnostic interfaces using `abc.ABC` and `@abstractmethod`.
* **Hybrid Architecture:** Supporting both cloud-based hosted LLMs (Google Gemini) and private on-device LLMs (Ollama).
* **Unit Testing with Mocks:** Verifying client wrapper behavior using isolated test providers without network or external model dependencies.
