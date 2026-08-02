
# Narrative Contradiction Engine (Pathway RAG)-

## 🎯 Overview
This project is an advanced Retrieval-Augmented Generation (RAG) system designed to evaluate character backstory consistency against extensive literary narratives exceeding 100,000 words. It acts as a logical reasoning engine, detecting causal contradictions between established lore and character claims.

## 🛠️ Architecture: "Decompose & Verify" Pipeline

### 1. Data Pipeline & Preprocessing
* **Streaming Ingestion:** Synchronizes raw novels (`.txt`) and backstories (`.csv`) directly from Google Drive using **Pathway's** live IO connectors.
* **Precision Chunking:** Mitigates context clipping using a sliding window approach (**1200 characters** with a **200-character overlap**).

### 2. Key Fact Extraction
* Backstories are broken down into verifiable "Anchor Claims" using keyword-based heuristics (e.g., *was, became, led, joined*) to filter out descriptive fluff.

### 3. Smart Evidence Retrieval (KNN)
* **Embeddings:** Claims and novel chunks are converted into 384-dimensional vectors using `all-MiniLM-L6-v2`.
* **Vector Store:** High-efficiency **NumPy Vector Store**.
* **Retrieval:** Executes a Top-5 KNN Search based on cosine similarity to retrieve the most relevant textual evidence.

### 4. Conflict Detection (NLI)
* Separates the Pathway data backbone from the PyTorch GPU reasoning layer.
* Utilizes `facebook/bart-large-mnli` to evaluate the retrieved evidence against the claim, outputting one of three labels:
  * ✅ **Supports**
  * ❌ **Contradicts**
  * ⚠️ **Insufficient Info**

---

## ⚡ Technical Optimizations

* **High Scalability:** Leverages Batch Encoding (size 32) to maximize GPU utilization, enabling the indexing of 100k+ word libraries in seconds.
* **Token Optimization:** Overcomes the NLI 512-token limit by exclusively merging the top 2 retrieved chunks and capping the input premise at 1500 characters.
* **Improved Accuracy:** Mitigates hard negative sensitivity by applying an Aggregation Logic with a strict **55% confidence threshold** before flagging a backstory as "Inconsistent."

---

## 💻 Tech Stack
* **Frameworks:** Pathway, PyTorch
* **NLP & Models:** Hugging Face Transformers, `all-MiniLM-L6-v2`, `facebook/bart-large-mnli`
* **Data Handling:** NumPy, Pandas

