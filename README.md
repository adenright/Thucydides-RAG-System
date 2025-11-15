# Thucydides-RAG-System
# **ThucydidesRAG: A Retrieval-Augmented Research Assistant for *The Peloponnesian War***

### **Author:** Anne-Duncan Enright  
### **Course:** AI Mini-Project Series — Mini-Project #3  
### **Purpose:** Real-World RAG Implementation for Humanities Research  

---

## 📘 **Project Overview**

**ThucydidesRAG** is a domain-specific Retrieval-Augmented Generation (RAG) system designed to support students, researchers, and instructors working with **Thucydides’ *History of the Peloponnesian War***.  
The system answers scholarly questions with **cited passages**, drawing on both the **Greek original** and **public-domain English translations**, along with targeted commentary and lecture notes.

This project demonstrates a realistic humanities-focused RAG pipeline, showcasing:

- Text ingestion & preprocessing (Book / Chapter / Section aligned)
- Hybrid retrieval (BM25 + vector embeddings)
- Chunking optimized for classical texts
- Citation-enforced generation
- Evaluation on real research questions (religion, stasis, speeches, major episodes)

This repository reflects the completed deliverable for **Mini-Project #3: Real-World RAG Implementation**.

---

## 🧩 **Use Case**

### **Primary Use Case**
**Research Assistant / Document Summarization**

The system helps answer questions such as:

- “Where does Thucydides describe the plague?”
- “Explain how religious breakdown relates to stasis in Corcyra.”
- “What does εἰκὸς mean in context?”
- “Compare how Thucydides treats Athens’ democracy differently in Books 2 and 8.”

### **Target Users**
- Advanced Greek students  
- Graduate researchers  
- Classicists  
- Digital humanities students  
- Instructors preparing lectures & handouts  

### **Success Criteria**
- ≥85% correct answers  
- ≥90% answers containing correct citations  
- ≤4s average latency  
- ≤5% hallucinated references  

---

## 📚 **Dataset & Sources**

### **Corpus Contents**
- **Thucydides (Greek text)** — Perseus Digital Library (TEI → TXT)
- **Thucydides (English translation)** — Public domain (Project Gutenberg)
- **Lecture notes & thematic handouts** — instructor-generated
- **Section metadata** — Book.Chapter.Section (e.g., *2.47.3*)

### **Formats**
- XML / TEI  
- TXT  
- MD  
- PDF (optional notes)

### **Constraints**
- Low-cost / OSS components  
- Optional local-only mode (for private instructor notes)  
- Must support non-technical users (simple UI)  

---

## 🏗️ **Architecture (MVP)**

**Pipeline:**  
`Ingestion → Chunking → Embeddings → Vector DB → Hybrid Retrieval → LLM → Citations`

### **Core Design Choices**

| Component | Choice | Rationale |
|----------|--------|-----------|
| Chunking | Section-aligned + overlap | Matches natural Thucydidean structure |
| Embeddings | Hosted multilingual embeddings | Handles Greek and English well |
| Vector DB | Chroma | Simple, persistent local store |
| Retrieval | Hybrid (BM25 + Vector) | Works for both lexical and semantic queries |
| LLM | Hosted GPT-class model | Best for academic reasoning |
| Citations | Mandatory | Ensures scholarly reliability |

---

## 🛠️ **Installation & Setup**

### **1. Clone the Repository**
```bash
git clone https://github.com/<your-username>/ThucydidesRAG.git
cd ThucydidesRAG
## 📦 Installation & Setup

### 2. Install Dependencies
```bash
pip install -r requirements.txt
3. Prepare the Corpus
Place your cleaned texts in:

bash
Copy code
/data/thucydides/
/data/notes/
4. Build the Index
bash
Copy code
python scripts/build_index.py
5. Run the Application
bash
Copy code
python app.py
🔍 Sample Code Snippets
Chunking Example
python
Copy code
def chunk_section(text, section_id, window=400, overlap=60):
    tokens = text.split()
    chunks = []
    start = 0
    while start < len(tokens):
        end = start + window
        chunk_tokens = tokens[start:end]
        chunk = {
            "id": f"{section_id}_{start}",
            "text": " ".join(chunk_tokens),
            "metadata": {"section": section_id}
        }
        chunks.append(chunk)
        start += (window - overlap)
    return chunks
Hybrid Retrieval Example
python
Copy code
results_bm25 = bm25.search(query, top_k=5)
results_vector = vector_store.similarity_search(query, k=5)

combined = rerank(results_bm25 + results_vector)
top_contexts = combined[:8]
LLM Prompt (with citation enforcement)
python
Copy code
prompt = f"""
Answer the question using ONLY the retrieved passages.
Cite Book.Chapter.Section for every claim.

Question: {query}

Context:
{format_context(top_contexts)}

Answer:
"""
🔎 Example Queries
“What sections narrate the plague at Athens?”

“How does Thucydides describe the moral collapse during stasis?”

“Where does he criticize oracles?”

“Explain the meaning of εἰκὸς in Book 3.”

Returns:

A concise academic answer

Cited Book/Chapter/Section references

Expandable snippets of Greek & English text

📊 Evaluation Summary
Total Questions: 18 (factual, thematic, Greek lexical, cross-book)

Metric	Result
Strict Correct Answers	13/18 (72%)
Correct + Partial	16/18 (89%)
Cited Answers	17/18 (94%)
Average Latency	3.2 seconds

Observations
Strong performance on:

Locating passages

Thematic interpretations grounded in text

Greek vocabulary questions

Most failures occurred with:

Very broad, synthetic questions

Inferences requiring multi-book synthesis

One citation hallucination (off-by-one error)

⚠️ Risks & Edge Cases
Technical Risks
OCR noise from scanned PDFs → messy chunks

Duplicate content (Greek text appears in multiple editions)

Vague queries can trigger generic LLM answers

Greek orthography variance (breathings, accents, alternate spellings)

Scholarly Risks
Misinterpretation of Thucydides’ arguments

Overreliance on generated summaries

Students treating AI interpretations as authoritative

Partial corpus may imply completeness

Operational Risks
Occasional citation hallucinations

Latency spikes from external APIs

🚀 Future Work
Retrieval Improvements
Cross-encoder reranker for better ranking

Multi-hop retrieval across Books 2, 3, 5, etc.

Greek morphological normalization (lemmatization)

UI Enhancements
Side-by-side Greek & English panels

Highlighted retrieved sections

Book/Chapter/Section filters

Indexing & Study Tools
Incremental indexing for fast ingestion of notes

Study Mode: retrieval-only to help students read Greek without LLM answers

📚 References
Thucydides. The History of the Peloponnesian War. Translated by Richard Crawley, Project Gutenberg, Release No. 7142, 2003.

DeepLearning.AI. Building and Evaluating Advanced RAG Applications.

mlsmall. RAG-Application-with-LangChain. GitHub, 2024.

Perseus Digital Library XML/TEI Corpus.

