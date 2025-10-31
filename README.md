You: "Tell me about Rudolph the Red-Nosed Reindeer"
Bot: "Rudolph the Red-Nosed Reindeer is a 1964 stop-motion
animated television special produced by Rankin/Bass.
It tells the story of Rudolph, a young reindeer with
a glowing red nose who saves Christmas..."

You: "When did it first air?"
Bot: "It first aired on NBC on December 6, 1964."

```

---

## 📚 Part 2: What You'll Learn

### **Technical Skills:**
1. **PDF Processing** - Loading and extracting text from documents
2. **Document Chunking** - Breaking large texts into digestible pieces
3. **Vector Embeddings** - Converting text to numbers for similarity search
4. **Vector Storage** - Storing and querying embeddings (ChromaDB)
5. **RAG Pipeline** - Complete indexing → retrieval → generation flow
6. **Streamlit UI** - Building a simple web interface
7. **Environment Management** - Working with API keys and .env files

### **RAG Concepts:**
- **Indexing Phase** - Prepare documents once
- **Retrieval Phase** - Find relevant information
- **Generation Phase** - Create natural language answers
- **End-to-End Flow** - How all pieces connect

### **Best Practices:**
- Project structure for RAG apps
- Separating concerns (data, processing, UI)
- Error handling basics
- User feedback

---

## 🏗️ Part 3: Architecture Overview

### **The Big Picture:**
```

┌─────────────────────────────────────────────────────┐
│ USER │
│ (Streamlit Interface) │
└────────────────────┬────────────────────────────────┘
│
│ Question: "Tell me about Rudolph"
↓
┌─────────────────────────────────────────────────────┐
│ RAG PIPELINE │
│ │
│ ┌──────────────┐ ┌──────────────┐ │
│ │ RETRIEVAL │───→│ GENERATION │ │
│ │ │ │ │ │
│ │ 1. Convert │ │ 1. Create │ │
│ │ question │ │ prompt │ │
│ │ to vector │ │ │ │
│ │ │ │ 2. Send to │ │
│ │ 2. Search │ │ LLM │ │
│ │ vector DB │ │ │ │
│ │ │ │ 3. Get │ │
│ │ 3. Return │ │ answer │ │
│ │ relevant │ │ │ │
│ │ chunks │ │ │ │
│ └──────────────┘ └──────────────┘ │
│ ↑ │
│ │ │
│ ┌──────────────┐ │
│ │ VECTOR DB │ │
│ │ (ChromaDB) │ │
│ │ │ │
│ │ Stores: │ │
│ │ - Document │ │
│ │ chunks │ │
│ │ - Embeddings│ │
│ │ - Metadata │ │
│ └──────────────┘ │
│ ↑ │
│ │ (Created once during indexing) │
│ │ │
│ ┌──────────────┐ │
│ │ INDEXING │ │
│ │ │ │
│ │ 1. Load PDF │ │
│ │ 2. Split │ │
│ │ 3. Embed │ │
│ │ 4. Store │ │
│ └──────────────┘ │
│ ↑ │
└─────────┼──────────────────────────────────────────┘
│
┌─────────┴─────────┐
│ holiday_shows.pdf│
│ │
│ Your knowledge │
│ base document │
└───────────────────┘

```

### **Data Flow:**

**Phase 1: Indexing (Run Once)**
```

PDF → Load → Split into chunks → Create embeddings → Store in ChromaDB

```

**Phase 2: Query (Run Each Time)**
```

User question → Embed question → Search ChromaDB → Get relevant chunks
↓
Retrieved chunks + Question → LLM → Natural language answer → User

```

---

## 📁 Part 4: File Structure

Here's what we'll build:
```

holiday-show-faq-bot/
├── .env # API keys (not in git)
├── .env.example # Template for API keys
├── .gitignore # What to exclude from git
├── pyproject.toml # Dependencies
├── README.md # Project documentation
├── .vscode/
│ └── settings.json # VS Code configuration
├── src/
│ ├── **init**.py
│ ├── indexer.py # Load PDF and create vector store
│ ├── retriever.py # Query the vector store
│ └── app.py # Streamlit UI (main entry point)
└── data/
├── raw/
│ └── holiday_shows.pdf # Your knowledge base
└── chroma_db/ # Vector database (created by indexer)

```

### **Why This Structure?**

- **`src/indexer.py`** - Separates data preparation (run once) from querying
- **`src/retriever.py`** - Encapsulates retrieval logic (reusable)
- **`src/app.py`** - User interface (what users interact with)
- **`data/raw/`** - Original documents (version controlled)
- **`data/chroma_db/`** - Generated database (not in git, can regenerate)

---

## 🎬 Part 5: How The Pieces Work Together

### **Step 1: Indexer (Run Once)**

**Purpose:** Prepare your knowledge base

**What it does:**
1. Reads `holiday_shows.pdf`
2. Splits text into ~500 token chunks
3. Creates embeddings for each chunk (using OpenAI)
4. Stores in ChromaDB

**When to run:**
- First time setup
- When you add/update documents

**Command:** `python -m src.indexer`

---

### **Step 2: Retriever (Used by App)**

**Purpose:** Find relevant information

**What it does:**
1. Takes a user question
2. Converts question to embedding
3. Searches ChromaDB for similar chunks
4. Returns top 4 most relevant chunks

**When it runs:** Every time user asks a question

**Not run directly** - used by `app.py`

---

### **Step 3: App (Main Interface)**

**Purpose:** User interaction

**What it does:**
1. Shows a chat interface
2. Gets user questions
3. Uses Retriever to find relevant info
4. Sends context + question to LLM
5. Displays answer to user

**When to run:** When you want to use the bot

**Command:** `streamlit run src/app.py`

---

## 🔄 The Complete Flow
```

SETUP (Once):

1. You create holiday_shows.pdf with information
2. Run indexer.py → Creates vector database

USAGE (Every time):

1. Run app.py → Opens browser with chat interface
2. User asks: "Tell me about Rudolph"
3. App uses retriever.py to find relevant chunks
4. App sends chunks + question to OpenAI
5. OpenAI generates natural answer
6. App shows answer to user
7. User asks follow-up → Repeat 3-6
