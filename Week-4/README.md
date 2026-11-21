# Week 4: Tokenizer Lab — Subword Tokenizer Comparison

## 1. Overview & Objective
This experiment examines and compares the internal mechanics of three prominent subword tokenization algorithms:
1. **Byte Pair Encoding (BPE)** represented by GPT-2 (`gpt2`)
2. **WordPiece** represented by Multilingual BERT (`bert-base-multilingual-cased`)
3. **SentencePiece (Unigram/BPE)** represented by XLM-RoBERTa (`xlm-roberta-base`)

The experiment evaluates token pieces, token IDs, and total token count across three languages: **English**, **Telugu**, and **Hindi**.

---

## 2. What the Experiment Does
1. **Loads Pretrained Tokenizers:** Uses the Hugging Face `transformers` library to fetch tokenizer configurations and vocabularies.
2. **Encodes Multilingual Inputs:** Evaluates identical sentences representing English, Telugu, and Hindi:
   * English: *"Artificial intelligence is changing the world."*
   * Telugu: *"కృత్రిమ మేధస్సు ప్రపంచాన్ని మారుస్తోంది."*
   * Hindi: *"कृत्रिम बुद्धिमत्ता दुनिया को बदल रही है।"*
3. **Inspects Subword Splits:** Converts token IDs back to human-readable subword tokens to show where and how words are split.
4. **Analyzes Vocabulary Efficiency:** Demonstrates why English-centric tokenizers produce high token inflation for Indic languages compared to multilingual tokenizers.

---

## 3. Directory Structure
```text
Week-4/
├── tokenizer_comparison.py   # Tokenizer evaluation script
├── requirements.txt          # Python dependencies
└── README.md                 # Experiment documentation
```

---

## 4. Setup & Configuration

### Dependencies (`requirements.txt`)
* `transformers`
* `sentencepiece`

### Install Dependencies
```powershell
cd "c:\KNOX\KLH\2nd Year\TRIMESTER-2\GEN AI\LAB_EXP\Week-4"
..\Myenv\Scripts\python.exe -m pip install -r requirements.txt
```

---

## 5. How to Run

```powershell
..\Myenv\Scripts\python.exe tokenizer_comparison.py
```

---

## 6. Experimental Results & Comparison Table

| Language | Input Text | BPE (GPT-2) | WordPiece (mBERT) | SentencePiece (XLM-R) |
| :--- | :--- | :---: | :---: | :---: |
| **English** | *Artificial intelligence is changing the world.* | **8 tokens** | **10 tokens** | **10 tokens** |
| **Telugu** | *కృత్రిమ మేధస్సు ప్రపంచాన్ని మారుస్తోంది.* | **112 tokens** | **18 tokens** | **14 tokens** |
| **Hindi** | *कृत्रिम बुद्धिमत्ता दुनिया को बदल रही है।* | **68 tokens** | **17 tokens** | **14 tokens** |

### Sample Output Snippet:
```text
===========================================================================
LANGUAGE: English
INPUT TEXT: Artificial intelligence is changing the world.
===========================================================================

BPE - GPT-2
Tokens     : ['Art', 'ificial', 'Ġintelligence', 'Ġis', 'Ġchanging', 'Ġthe', 'Ġworld', '.']
Token IDs  : [8001, 9542, 4430, 318, 5609, 262, 995, 13]
Token count: 8

===========================================================================
LANGUAGE: Telugu
INPUT TEXT: కృత్రిమ మేధస్సు ప్రపంచాన్ని మారుస్తోంది.
===========================================================================

BPE - GPT-2
Tokens     : ['à', '°', 'ķ', 'à', '±', 'ĥ', 'à', '°', ...]
Token count: 112

SentencePiece - XLM-R
Tokens     : ['<s>', ' ', 'కృత', '్రి', 'మ', ' మే', 'ధ', 'స్సు', ' ప్రపంచ', 'ాన్ని', ' మారు', 'స్తోంది', '.', '</s>']
Token count: 14
```

---

## 7. Key Findings & Theoretical Insights
* **Token Inflation:** GPT-2 was trained almost entirely on English text; its vocabulary lacks dedicated Indic characters. Consequently, Telugu and Hindi characters fall back to multi-byte UTF-8 representations, multiplying the token count by 8x to 10x.
* **SentencePiece Efficiency:** XLM-RoBERTa uses a large multilingual vocabulary trained on raw Unicode text directly, grouping full syllables and morphemes together and producing only 14 tokens for Telugu.
* **Special Tokens:** Multilingual BERT adds `[CLS]` and `[SEP]`, while XLM-RoBERTa uses `<s>` and `</s>`.
