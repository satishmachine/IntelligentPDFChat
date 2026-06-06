# 🧠 DocuChat AI — Conversational RAG Q&A with PDF & Chat History

> **Talk to your documents. Remember your conversations.**
> An end-to-end Retrieval-Augmented Generation (RAG) chatbot that lets you upload PDFs and have intelligent, context-aware conversations — powered by Groq's blazing-fast LLMs and LangChain's advanced memory chains.

---

## ✨ Features

- 📄 **Multi-PDF Upload** — Upload one or more PDF documents directly through the browser UI
- 🧠 **Conversational Memory** — Full chat history maintained per session using LangChain's `RunnableWithMessageHistory`
- 🔍 **Context-Aware Retrieval** — History-aware retriever reformulates follow-up questions before searching the vector store
- ⚡ **Groq LLM Backend** — Uses `qwen/qwen3-32b` via Groq for ultra-fast, high-quality answers
- 🗃️ **ChromaDB Vector Store** — Documents are chunked and embedded locally using `all-MiniLM-L6-v2` (HuggingFace)
- 🔐 **Secure API Key Entry** — Groq API key entered at runtime via password field, never hardcoded
- 🖥️ **Streamlit UI** — Clean, interactive web interface with zero frontend code needed
- 🔄 **Multi-Session Support** — Manage multiple independent chat sessions with custom Session IDs

---

## 🏗️ Architecture

```
PDF Upload (Streamlit UI)
        │
        ▼
PyPDFLoader → RecursiveCharacterTextSplitter (chunk_size=5000, overlap=500)
        │
        ▼
HuggingFace Embeddings (all-MiniLM-L6-v2) → ChromaDB Vector Store
        │
        ▼
History-Aware Retriever (reformulates questions using chat history)
        │
        ▼
Groq LLM (qwen3-32b) ← Stuff Documents Chain + QA Prompt
        │
        ▼
RunnableWithMessageHistory → Session-Scoped Chat Memory
        │
        ▼
Streamlit Chat Interface (Answer + Chat History Display)
```

---

## 🚀 Getting Started

### Prerequisites

- Python **3.12+**
- A **Groq API Key** — get one free at [console.groq.com](https://console.groq.com)
- A **HuggingFace API Token** — get one at [huggingface.co/settings/tokens](https://huggingface.co/settings/tokens)

---

### 1. Clone the Repository

```bash
git clone https://github.com/YOUR_USERNAME/docuchat-ai-rag.git
cd docuchat-ai-rag
```

---

### 2. Set Up the Virtual Environment

**Using `uv` (recommended — blazing fast):**
```bash
pip install uv
uv venv
.venv\Scripts\activate        # Windows
# source .venv/bin/activate   # macOS/Linux
uv pip install -r requirements.txt
```

**Using standard `pip`:**
```bash
python -m venv .venv
.venv\Scripts\activate        # Windows
# source .venv/bin/activate   # macOS/Linux
pip install -r requirements.txt
```

---

### 3. Configure Environment Variables

Create a `.env` file in the project root:

```env
HUGGINGFACEHUB_API_TOKEN=your_huggingface_token_here
```

> ⚠️ **Note:** Your Groq API key is entered directly in the app UI at runtime — it is NOT stored in `.env`.

---

### 4. Run the Application

```bash
streamlit run app.py
```

The app will open automatically at `http://localhost:8501`

---

## 🖼️ Usage Guide

1. **Enter your Groq API Key** in the password field at the top
2. **Set a Session ID** (or use the default) to manage your conversation context
3. **Upload one or more PDF files** using the file uploader
4. **Type your question** in the input box and press Enter
5. The assistant will answer using content from your PDFs, while **remembering your full conversation history**

---

## 📁 Project Structure

```
docuchat-ai-rag/
│
├── app.py                  # Main Streamlit application (RAG pipeline + UI)
├── main.py                 # Entry point stub
├── requirements.txt        # Python package dependencies
├── pyproject.toml          # Project metadata (uv/PEP 517)
├── .env                    # Environment variables (not committed to Git)
├── .gitignore              # Git ignore rules
├── .python-version         # Python version pin (3.12)
└── README.md               # This file
```

---

## 🧰 Tech Stack

| Layer | Technology |
|---|---|
| **UI** | [Streamlit](https://streamlit.io/) |
| **LLM** | [Groq](https://groq.com/) — `qwen/qwen3-32b` |
| **Embeddings** | [HuggingFace](https://huggingface.co/) — `all-MiniLM-L6-v2` |
| **Vector Store** | [ChromaDB](https://www.trychroma.com/) |
| **RAG Framework** | [LangChain](https://www.langchain.com/) |
| **PDF Parsing** | [PyPDF](https://pypdf.readthedocs.io/) |
| **Memory** | `RunnableWithMessageHistory` (LangChain Core) |
| **Package Manager** | [uv](https://github.com/astral-sh/uv) |
| **Environment** | Python 3.12+ |

---

## 🔧 Key Dependencies

```
langchain >= 1.3.4
langchain-groq >= 1.1.2
langchain-huggingface >= 1.2.2
langchain-chroma >= 1.1.0
langchain-classic >= 1.0.7
langchain-community >= 0.4.2
langchain-core >= 1.4.0
langchain-text-splitters >= 1.1.2
streamlit >= 1.58.0
pypdf >= 6.13.0
sentence-transformers >= 5.5.1
python-dotenv >= 1.2.2
faiss-cpu >= 1.14.2
```

---

## ⚙️ How the RAG Pipeline Works

### Step 1 — Document Ingestion
PDFs are loaded with `PyPDFLoader` and split into chunks of **5,000 characters** with **500-character overlap** using `RecursiveCharacterTextSplitter`.

### Step 2 — Embedding & Indexing
Each chunk is embedded locally using `all-MiniLM-L6-v2` (no API call needed) and stored in a **ChromaDB** in-memory vector store.

### Step 3 — History-Aware Retrieval
When a user asks a follow-up question, the **history-aware retriever** uses the LLM to first reformulate the question as a standalone query (incorporating context from chat history), then retrieves relevant chunks.

### Step 4 — Answer Generation
Retrieved context chunks are passed to the Groq LLM via a **Stuff Documents Chain** with a concise QA prompt. Answers are kept to **3 sentences maximum** for clarity.

### Step 5 — Memory Management
All messages are stored per `session_id` in `st.session_state`, enabling persistent, multi-turn conversations within a single Streamlit session.

---

## 🛡️ Security Notes

- ✅ Groq API key entered at runtime — never stored to disk
- ✅ HuggingFace token loaded from `.env` (excluded from Git via `.gitignore`)
- ✅ Uploaded PDFs are written to a temporary file and not persisted between sessions
- ⚠️ Add `.env` to `.gitignore` before pushing to a public repository

---

## 🤝 Contributing

Contributions are welcome! Feel free to open issues or pull requests for:
- 🐛 Bug fixes
- ✨ New LLM model support
- 🎨 UI/UX improvements
- 📦 Persistent vector store (e.g., saving embeddings between sessions)

---

## 📜 License

This project is licensed under the **MIT License** — see [LICENSE](LICENSE) for details.

---

<div align="center">
  <sub>Built with ❤️ using LangChain · Groq · ChromaDB · Streamlit</sub>
</div>
