# ⚖️ LexAI — AI-Powered Legal Assistant for Indian Law

> An intelligent, multi-stage AI pipeline that classifies legal queries, retrieves context-aware answers via RAG, falls back to live web search, and generates structured legal guidance using Groq LLM — all orchestrated through a LangGraph workflow.

[![Python](https://img.shields.io/badge/Python-3.10+-blue?logo=python)](https://python.org)
[![LangGraph](https://img.shields.io/badge/LangGraph-Workflow-orange)](https://github.com/langchain-ai/langgraph)
[![Groq](https://img.shields.io/badge/Groq-LLM-green)](https://groq.com)
[![Pinecone](https://img.shields.io/badge/Pinecone-VectorDB-purple)](https://www.pinecone.io)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow)](LICENSE)

---

## 🚀 What Is LexAI?

**LexAI** is a production-grade AI Legal Assistant that helps users navigate Indian law — from understanding their legal rights to planning actionable next steps. It combines **Retrieval-Augmented Generation (RAG)**, **live web search (SerpAPI)**, and **Groq's fast LLM inference** into a single, intelligent pipeline.

### 🎯 Key Capabilities

- 🏛️ **Domain Classification** — Routes legal vs. non-legal queries intelligently
- ⚠️ **Risk Assessment** — Classifies query risk as Low / Medium / High
- 📚 **RAG Engine** — Semantic search over vectorized Indian legal documents
- 🌐 **SerpAPI Fallback** — Live web context when RAG confidence is low
- 🤖 **LLM Synthesis** — Groq-powered structured legal response generation
- 🗺️ **Action Roadmap** — Step-by-step legal procedure guidance
- 👨‍⚖️ **Lawyer Recommendation** — Yes / Optional / No, with reasoning
- 🛡️ **Built-in Disclaimer** — Responsible AI legal guidance

---

## 🧠 Architecture Overview

### LangGraph Workflow Orchestration

The pipeline is modeled as a **Directed Acyclic Graph (DAG)** using LangGraph, where each node performs a discrete task and conditional edges handle smart routing.

```
[User Query]
     │
     ▼
[Domain Classifier Node]
     │
     ├── Non-Law Query ──────────────────────► [Direct LLM Response]
     │
     └── Law Query
          │
          ▼
     [Query Refinement Node]          ← Restructures raw query into formal legal query
          │
          ▼
     [Risk Classifier Node]           ← Tags risk: Low / Medium / High
          │
          ▼
     [RAG Retrieval Node]             ← Semantic search over Pinecone vector store
          │
          ├── Score ≥ Threshold ──────► [RAG Context Ready]
          │
          └── Score < Threshold ──────► [SerpAPI Fallback Node]  ← Live web search
                                              │
                                              ▼
                                     [Context Merge Node]         ← RAG + SERP merged
                                              │
                                              ▼
                                     [Legal Expert LLM Node]      ← Groq inference
                                              │
                                              ▼
                                     [Structured Output]
                                       ├── Legal Explanation
                                       ├── Action Roadmap
                                       ├── Lawyer Requirement
                                       └── Disclaimer
```

---

## 🧩 Core Components

### 1. 🔍 Domain Classifier
- Determines if a query is **law-related** or **general**
- Legal queries proceed through the full pipeline
- Non-legal queries get a direct LLM response for efficiency

### 2. ✏️ Query Refiner
- Transforms raw user input into a **structured legal query**
- Adds jurisdiction context (Indian Law)
- Improves retrieval precision

### 3. ⚠️ Risk Classifier
- Categorizes the legal scenario as:
  - 🟢 **Low** — Informational, no immediate legal threat
  - 🟡 **Medium** — Procedural concern, may require legal advice
  - 🔴 **High** — Urgent, rights at risk, lawyer strongly recommended

### 4. 📚 RAG Engine
- Performs **cluster-aware semantic search** over Pinecone
- Returns top-k relevant legal document chunks
- Threshold-based confidence gating — only uses RAG if score is high enough

### 5. 🌐 SerpAPI Fallback
- Activates when RAG similarity score is below threshold
- Fetches live, up-to-date legal information from the web
- Provides real-world context for niche or recent legal issues

### 6. 🤖 Legal Expert LLM (Groq)
- Final synthesis node powered by **Groq's ultra-fast LLM inference**
- Receives merged context (RAG + SERP) + risk classification
- Generates a 4-part structured response

### 7. 🗺️ Roadmap Generator *(within LLM node)*
- Produces numbered, actionable steps tailored to the user's legal situation
- Guides users through evidence collection, filing procedures, and escalation

---

## 🗄️ Data Pipeline

```
Legal PDF Documents
        │
        ▼
[PyPDFLoader]               ← Load raw text from legal documents
        │
        ▼
[Sentence Splitter]         ← Split into sentences/passages
        │
        ▼
[SentenceTransformer]       ← Generate dense embeddings (all-MiniLM-L6-v2)
        │
        ▼
[KMeans Clustering]         ← Group semantically similar chunks
        │
        ▼
[Pinecone Upsert]           ← Store vectors with cluster metadata
        │
        ▼
[Pinecone Index]            ← Ready for semantic retrieval
```

---

## 🔍 Retrieval Strategy

### Semantic Search
- Uses `sentence-transformers` to embed the refined user query
- Computes cosine similarity against stored Pinecone vectors
- Returns `top-k` most relevant legal chunks

### Cluster-Based Retrieval
- Documents are pre-clustered using **KMeans** during ingestion
- Cluster IDs stored as metadata in Pinecone
- Enables **namespace/cluster-filtered** retrieval for precision:
  - Query is assigned to its nearest cluster
  - Retrieval is scoped to that cluster for higher relevance

---

## 🧠 AI Features

| Feature | Description |
|---|---|
| **Legal Reasoning** | Contextual, multi-step legal analysis using Groq LLM |
| **Risk Detection** | Severity classification based on query semantics |
| **Hybrid Retrieval** | RAG + live web search for maximum coverage |
| **Context-Aware Answers** | Merged context ensures grounded, factual responses |
| **Structured Output** | Consistent format: answer + roadmap + lawyer flag + disclaimer |
| **Query Refinement** | LLM pre-processes raw queries for better retrieval accuracy |

---

## ⚡ Tech Stack

| Layer | Technology |
|---|---|
| **Workflow Orchestration** | LangGraph |
| **LLM Inference** | Groq (Llama 3 / Mixtral) |
| **Embeddings** | SentenceTransformers (`all-MiniLM-L6-v2`) |
| **Vector Database** | Pinecone |
| **Clustering** | Scikit-learn (KMeans) |
| **Web Search Fallback** | SerpAPI |
| **Document Loader** | PyPDF (LangChain) |
| **LangChain Modules** | langchain, langchain-community, langchain-groq, langchain-huggingface |
| **Environment Management** | python-dotenv |
| **Notebook Runtime** | Jupyter / IPython |
| **Language** | Python 3.10+ |

---

## 📂 Project Structure

```
law_suggestion/
│
├── agent.ipynb              # Main LangGraph pipeline (all nodes + workflow)
├── requirement.txt          # All Python dependencies
├── .env                     # API keys (Groq, Pinecone, SerpAPI) — not committed
├── .gitignore               # Ignores venv, .env, caches
│
└── venv/                    # Python virtual environment (not tracked)
```

---

## 🔄 End-to-End Execution Flow

1. **User submits a query** (e.g., *"My landlord locked me out. What are my rights?"*)
2. **Domain Classifier** identifies it as a legal query → proceeds to full pipeline
3. **Query Refiner** restructures it: *"Tenant rights during illegal eviction under Indian Rent Control Act"*
4. **Risk Classifier** tags it as 🔴 **High Risk** (rights being actively violated)
5. **RAG Node** embeds the refined query and searches Pinecone
   - If similarity score ≥ threshold → uses retrieved legal document chunks
   - Else → **SerpAPI Fallback** fetches live web results
6. **Context Merge** combines RAG and SERP results into a single context block
7. **Legal Expert LLM** (Groq) processes context + risk level and generates:
   - ✅ Legal Explanation (what the law says)
   - 🗺️ Action Roadmap (steps to take)
   - 👨‍⚖️ Lawyer Needed: **Yes** (with justification)
   - ⚠️ Disclaimer (AI is not a substitute for legal counsel)
8. **Response returned** to user in structured, readable format

---

## 📝 Sample Execution

**Question**: *"What are the rights of women during police investigation?"*

### 🛠️ Agent Execution Logs
```text
🔍 Refined Query: "Rights of women during police investigation under Indian Law, Section 160 and 161 of CrPC, and guidelines by High Court of India"
⚖️ Risk Assessment: LOW
🎯 Best cluster matched: Cluster 14. Going deeper...
✅ High confidence (0.64). Moving to Roadmap...
🗺️ Generating legal roadmap...
⚖️ Finalizing expert response (Strictly Indian Law)...

--- AGENT EXECUTION COMPLETE ---
```

### ⚖️ Final Expert Response

> **Disclaimer**: The information provided is for general guidance only and should not be considered as legal advice. It is recommended to consult a qualified lawyer for specific and personalized advice.
> 
> **Lawyer Requirement**: **Optional** - While a lawyer is not mandatory, it is highly recommended to have one for proper guidance and representation during the police investigation.
> 
> **Legal Summary**: In India, women have the right to be treated with dignity and respect during police investigations. The police are required to follow certain procedures to ensure that women are not harassed or intimidated during the investigation process.
> 
> **Relevant Laws**: The Indian Penal Code (IPC), 1860, and the Code of Criminal Procedure (CrPC), 1973, are relevant laws that govern the investigation process.
> 
> **Step-by-step Action Roadmap**:
> 1. If a woman wants to file a complaint, she can approach the police station and provide a written complaint.
> 2. The police are required to register a First Information Report (FIR) under Section 154 of the CrPC.
> 3. The police will then conduct an investigation, during which they may ask questions and collect evidence.
> 4. The woman has the right to have a lawyer present during the investigation.
> 5. The police are required to provide a copy of the FIR to the woman and keep her informed about the progress of the investigation.
> 
> **Important Rights / Notes**:
> - The right to dignity and respect during the investigation process.
> - The right to have a lawyer present during the investigation.
> - The right to receive a copy of the FIR.
> - The right to be kept informed about the progress of the investigation.
> - The police are required to provide assistance to the woman if she chooses to file a complaint under the Indian Penal Code or any other law.
> 
> **Final Note**: It is essential to remember that the police investigation process should be fair, impartial, and respectful. If a woman feels that her rights are being violated or that she is being harassed during the investigation, she should immediately seek help from a lawyer or a women's rights organization.

---

## 🛡️ Safety & Guardrails

### Legal Disclaimer
Every response includes a mandatory disclaimer:
> *"This response is generated by an AI system and is for informational purposes only. It does not constitute legal advice. Please consult a qualified legal professional for your specific situation."*

### Risk-Based Escalation
- **High Risk** → Always recommends consulting a lawyer
- **Medium Risk** → Recommends optional legal consultation
- **Low Risk** → Self-service guidance provided

### RAG Confidence Gating
- Responses are only sourced from RAG if retrieval score exceeds a defined threshold
- Below threshold: falls back to SerpAPI, preventing hallucinated legal answers

---

## 🔧 Setup & Installation

```bash
# 1. Clone the repository
git clone https://github.com/himanshu9330/LAW_chat_bot.git
cd LAW_chat_bot

# 2. Create and activate virtual environment
python -m venv venv
venv\Scripts\activate        # Windows
source venv/bin/activate     # macOS/Linux

# 3. Install dependencies
pip install -r requirement.txt

# 4. Configure environment variables
# Create a .env file with the following:
GROQ_API_KEY=your_groq_api_key
PINECONE_API_KEY=your_pinecone_api_key
SERPAPI_API_KEY=your_serpapi_key

# 5. Launch the notebook
jupyter notebook agent.ipynb
```

---

## 🚀 Future Improvements

| Feature | Description |
|---|---|
| 🌐 **Multi-language Support** | Hindi, Tamil, Bengali legal queries via translation layer |
| 📜 **Larger Legal Corpus** | Include IPC, CrPC, Constitution, Consumer Protection Act |
| 🔄 **Real-time Legal Updates** | Automated ingestion of new judgements and amendments |
| 🖥️ **Web UI** | Streamlit or Next.js frontend for end-user interaction |
| 📊 **Analytics Dashboard** | Query trends, risk distribution, common legal domains |
| 🔐 **User Authentication** | Secure sessions and query history |
| 🧪 **Evaluation Framework** | RAGAS-based RAG evaluation and LLM output scoring |
| 📱 **Mobile App** | React Native app for on-the-go legal assistance |

---

## 👨‍💻 Author

**Himanshu** — AI/ML Developer
- GitHub: [@himanshu9330](https://github.com/himanshu9330)

---

## 📄 License

This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for details.

---

> ⚠️ **Disclaimer**: LexAI is an AI-powered tool for informational purposes only. It does not constitute legal advice. Always consult a qualified legal professional for matters affecting your rights.
