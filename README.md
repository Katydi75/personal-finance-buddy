# Personal Finance Buddy

An AI-powered **financial coaching assistant**. It answers personal-finance questions using a knowledge base built from YouTube courses (RAG), builds step-by-step house / savings / trip plans, evaluates the user's financial health and pulls live data (e.g. mortgage rates) from the web.

## 🚀 Features

- **Data ingestion**
  - Downloads YouTube audio with `yt-dlp`.
  - Transcribes English videos and translates French ones to English with OpenAI **Whisper**.
  - Cleans and chunks transcripts (500 characters, 100 overlap).
  - Embeds chunks with OpenAI embeddings and stores them in **ChromaDB** (built once, then reused).

- **RAG**
  - GPT-4o with low temperature.
  - History-aware retriever: follow-up questions are rewritten into standalone questions before retrieval.
  - Strict "answer only from the context" prompt.

- **Agent** (OpenAI tools agent) with three tools:
  - `knowledge_base` → answers finance concepts from the YouTube transcripts (RAG).
  - `create_plan` → builds house / savings / trip plans; the LLM extracts every detail the user gives, in any order, and the tool asks for the next missing one.
  - `web_search` → live data such as current mortgage rates (DuckDuckGo).
  - Financial health score (**Strong / Moderate / Weak**) based on savings rate, debt / annual income and emergency-fund months, with explanations.
  - Refuses personalized investment advice and answers in the user's language.

- **Evaluation**
  - Keyword self-tests and multi-turn scenarios (including a user correcting earlier information).
  - LLM-as-judge on a LangSmith dataset (correctness, clarity, helpfulness), with full LangSmith tracing.

- **Gradio app**
  - Chat by text or voice (speech-to-text).
  - Live user profile panel, one isolated session per visitor.
  - Export the conversation as PDF and a financial report as Excel.

## 🛠️ Tech Stack

LangChain · OpenAI (GPT-4o, embeddings) · ChromaDB · Whisper · yt-dlp · DuckDuckGo Search · LangSmith · Gradio

## 📂 Project Structure

```
├── main.ipynb          # The whole pipeline, step by step
├── requirements.txt
├── .env.example        # Template for your API keys
└── data/               # Created at runtime (audio, transcripts, chroma_db) – not committed
```

Notebook cells:
1. **Data ingestion**: YouTube → Whisper → ChromaDB
2. **RAG setup**: retriever + QA chain
3. **Agent setup**: tools, financial plans, financial health
4. **Evaluation**: self-tests, multi-turn scenarios, LangSmith
5. **Gradio UI**

## ▶️ How to Run

Requirements: Python 3.12 and [ffmpeg](https://ffmpeg.org/) (macOS: `brew install ffmpeg`).

```bash
git clone https://github.com/<your-username>/personal-finance-buddy.git
cd personal-finance-buddy

python3.12 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
python -m ipykernel install --user --name personal-finance-buddy

cp .env.example .env   # then add your API keys
```

Open `main.ipynb`, select the **personal-finance-buddy** kernel and run the cells in order.

The first run downloads and transcribes the videos (a few minutes). Later runs reuse `data/chroma_db`; delete that folder to rebuild the knowledge base.

## ⚠️ Disclaimer

This project is for educational purposes only and does not provide personalized financial advice.
