# Corrective RAG AI Agent

An experimental, notebook based Corrective Retrieval Augmented Generation (CRAG) pipeline. It answers questions using the included deep learning book, checks whether retrieved passages are relevant, and uses web search when the book does not provide enough evidence.

## How it works

The workflow in [`rag.ipynb`](rag.ipynb) does the following:

1. Loads `Book/Deep_learning.pdf`, splits it into overlapping text chunks, and indexes them in a FAISS vector store using OpenAI embeddings.
2. Retrieves the four closest chunks for a question and asks an LLM to score their relevance.
3. If a chunk is confidently relevant, uses the book passages. Otherwise, rewrites the question as a search query and retrieves web results with Tavily. For uncertain retrievals, it combines book and web results.
4. Filters the context to relevant sentences and asks the LLM to answer using that context.

## Requirements

- Python 3.11 (the notebook kernel metadata currently specifies Python 3.11.1)
- An OpenAI API key
- A Tavily API key for the web search path
- Jupyter Notebook or JupyterLab

## Setup

Run these commands from the repository root.

```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
pip install -r requirements.txt
```

Create a `.env` file in the repository root with your own keys:

```dotenv
OPENAI_API_KEY=your_openai_api_key
TAVILY_API_KEY=your_tavily_api_key
```

Keep `.env` private; it is excluded from version control. The notebook loads it with `python-dotenv`.

## Run

Start Jupyter from the repository root, open `rag.ipynb`, and run its cells in order:

```powershell
jupyter notebook
```

Set the question in the final example cell. The PDF loader currently uses an absolute Windows path. When running from a different location or machine, update the path in that cell to point to your local `Book/Deep_learning.pdf` file.

The notebook builds the FAISS index in memory each time it runs, so the first indexing step may take time and incur OpenAI embedding API usage. Model calls also use the OpenAI API; the fallback search uses Tavily.

## Configuration notes

- The chat model is `gpt-4o-mini` and the embedding model is `text-embedding-3-large`, configured in the notebook.
- Retrieval returns four chunks by default.
- Relevance thresholds (`UPPER_TH` and `LOWER_TH`) are set in the notebook and control whether the pipeline trusts the book, searches the web, or combines both sources.
- `requirements.txt` lists the Python packages used by the notebook.
