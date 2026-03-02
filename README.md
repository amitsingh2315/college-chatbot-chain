# 🎓 College AI Chatbot — Agentic RAG System

An intelligent multi-agent chatbot that answers college-related queries using **WhatsApp chat data**, **student CSV database**, and **multiple PDF documents** — powered by **Groq LLM** and **LangChain**.

---

<img width="1432" height="1076" alt="Screenshot 2026-02-25 104809" src="https://github.com/user-attachments/assets/b53fb3f1-d9f9-41a1-8bf6-76bbbd055df8" />


<img width="1951" height="1771" alt="image" src="https://github.com/user-attachments/assets/82965e02-f275-4378-935a-b1e9d27431b4" />



## 📁 Project Structure

```
college_chatbot/
│
├── .env                                  # API Keys (Groq, HuggingFace, etc.)
│
├── 📂 Data Files
│   ├── chat_data.txt                     # WhatsApp group chat export
│   ├── akgec_data.csv                    # Student database (Roll No, Marks, etc.)
│   ├── akgec_webdata.pdf                 # College info PDF
│   └── pdf_file/                         # 📂 Multiple PDF folder
│       ├── AdmissionBrochure2024-25.pdf
│       ├── AKG-Placement-Brochure-2024_25.pdf
│       ├── akgec liberary user manual.pdf
│       ├── akgec_webdata.pdf
│       ├── AKGEC-Mandatory-Disclosure-_2025-26.pdf
│       ├── CSR-Brochure.pdf
│       ├── ECO-friendly-brochure.pdf
│       ├── EMERGENCE-22-24.pdf
│       ├── Information-Booklet-2025-26.pdf
│       ├── NIRF-ENGINEERING-2025.pdf
│       └── NIRF-INNOVATION-2025.pdf
│
├── 📓 Notebooks
│   ├── college_chatbot.ipynb             # v1 — Router Agent approach
│   ├── college_chatbot_chain.ipynb       # v2 — Chain Rule (single PDF)
│   └── college_chatbot_multipdf_chain.ipynb  # v3 — Chain Rule (multi-PDF) ⭐
│
├── 📂 Vector Databases (auto-created)
│   ├── pdf_db/                           # ChromaDB for v1
│   ├── chat_db_chain/                    # ChromaDB for WhatsApp (v2/v3)
│   ├── pdf_db_chain/                     # ChromaDB for v2
│   └── pdf_db_multipdf_chain/            # ChromaDB for v3
│
└── README.md
```

---

## 🤖 Three Notebook Versions

### 1️⃣ `college_chatbot.ipynb` — Router Agent

- Uses an **LLM-powered Router Agent** to decide which agent to call
- Query → Router → `PDF_AGENT` / `WHATSAPP_AGENT` / `STUDENT_AGENT`
- Single PDF support

### 2️⃣ `college_chatbot_chain.ipynb` — Chain Rule (Single PDF)

- **No router** — deterministic sequential chain
- Order: `WhatsApp → Student → PDF`
- If answer found → stop; else → next agent
- Single PDF support

### 3️⃣ `college_chatbot_multipdf_chain.ipynb` — Chain Rule (Multi-PDF) ⭐

- Same chain logic as v2
- **Loads ALL PDFs** from `pdf_file/` folder automatically
- Triple-layer keyword search + vector search
- **Recommended version**

---

## 🧠 Architecture

```
User Query
  │
  ├─→ WhatsApp Agent (chat_data.txt → ChromaDB)
  │     └─ If answer found → STOP ✅
  │
  ├─→ Student Agent (akgec_data.csv → Pandas)
  │     └─ If answer found → STOP ✅
  │
  └─→ PDF Agent (pdf_file/*.pdf → ChromaDB)
        └─ If answer found → STOP ✅
              │
              └─ If no data → "Data available nahi hai."
```

---

## 🔧 Agents Overview

| Agent | Data Source | Technology | Purpose |
|-------|-----------|------------|---------|
| **WhatsApp Agent** | `chat_data.txt` | LangChain + ChromaDB | Chat analysis: who talked most, abused most, etc. |
| **Student Agent** | `akgec_data.csv` | Pandas + LLM JSON parser | Student info: marks, phone, email, branch |
| **PDF Agent** | `pdf_file/*.pdf` | PyPDFLoader + ChromaDB | College info: fees, timing, faculty, placement |

---

## 🛠️ Tech Stack

| Component | Tool |
|-----------|------|
| **LLM** | Groq API — `llama3-70b-8192` |
| **Embeddings** | HuggingFace — `sentence-transformers/all-MiniLM-L6-v2` |
| **Vector DB** | ChromaDB (persistent) |
| **PDF Loader** | LangChain `PyPDFLoader` |
| **CSV Engine** | Pandas |
| **Framework** | LangChain |

---

## ⚡ Quick Start

### 1. Clone & Setup

```bash
cd college_chatbot
```

### 2. Create `.env` file

```env
GROQ_API_KEY=your_groq_api_key_here
HUGGINGFACEHUB_API_TOKEN=your_hf_token_here
```

### 3. Install Dependencies

```bash
pip install langchain langchain-community chromadb pandas pypdf sentence-transformers langchain-groq langchain-huggingface python-dotenv
```

### 4. Run Notebook

Open `college_chatbot_multipdf_chain.ipynb` in VS Code / Jupyter and run all cells (1–9).

---

## 💬 Example Queries

| Query | Routed To | Sample Answer |
|-------|-----------|---------------|
| `"library timing"` | PDF Agent | Library ka timing Monday se Saturday 8:30 AM se 9 PM tak hai. |
| `"Shobhit ka number"` | Student Agent | Shobhit Srivastava ka contact no hai: 9369807523 |
| `"who abused most"` | WhatsApp Agent | Aditya ne sabse zyada gaali di hai group me. |
| `"hostel fees"` | PDF Agent | Boys hostel Double Seater Rs. 140000 hai. |
| `"who is S.L. Kapoor"` | PDF Agent | S.L. Kapoor AKGEC ke founder chairman hain. |
| `"placement statistics"` | PDF Agent | 2024 me highest package 44 LPA raha hai. |

---

## 🔍 PDF Search Strategy (Triple-Layer)

```
Query → Layer 1: Exact substring match
          ↓ (not found)
        Layer 2: All key tokens match
          ↓ (not found)
        Layer 3: Partial token match (50%+)
          ↓ (not found)
        Layer 4: ChromaDB vector search (top 5)
          ↓ (not found)
        "Data available nahi hai."
```

---

## 📌 Key Features

- ✅ **Agentic AI System** — auto routes to correct agent
- ✅ **Hinglish Answers** — responds in Hindi + English mix
- ✅ **Multi-PDF Support** — loads all PDFs from folder
- ✅ **Persistent ChromaDB** — no re-indexing needed
- ✅ **Hybrid Search** — keyword + vector for best accuracy
- ✅ **Groq LLM** — fast inference with llama3-70b
- ✅ **Chain Rule** — deterministic, no hallucination on routing

---

## 👤 Author

**Amit Kumar**

B.Tech — AKGEC

---
