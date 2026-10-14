# RAG Handbook Basics

A hands-on Retrieval-Augmented Generation (RAG) project that answers questions using content retrieved from NASA's *Systems Engineering Handbook*. The notebook uses LangChain, Ollama, and ChromaDB to run the pipeline locally.

## What this project does

1. Loads the PDF handbook into page-level documents.
2. Splits the text into overlapping chunks.
3. Creates embeddings with `nomic-embed-text`.
4. Stores chunks and vectors in a persistent ChromaDB collection.
5. Retrieves relevant passages for a question.
6. Uses the local `phi3:mini` model to generate an answer grounded in the retrieved context.
7. Tests a retrieval-distance threshold intended to refuse likely out-of-scope questions.

> **Important:** The prompt and distance threshold are heuristics, not a guarantee against hallucinations. Evaluate the system using covered and uncovered questions and tune the threshold for your data.

## Project files

```text
rag-handsbook-basics/
├── README.md
├── notebook/
│   └── RAG_Handson.ipynb
├── nasa_systems_engineering_handbook.pdf  # add locally; not included in this repository
└── chroma_db/                              # generated locally when indexing
```

## Requirements

- Python 3.10+ (a recent Python version is recommended)
- [Ollama](https://ollama.com/download)
- VS Code with the Python and Jupyter extensions, or another Jupyter environment
- The NASA handbook PDF file

## Setup

### 1. Clone the repository

```bash
git clone https://github.com/abhi1207-chugh/rag-handsbook-basics.git
cd rag-handsbook-basics
```

### 2. Create and activate a virtual environment

macOS / Linux:

```bash
python3 -m venv .venv
source .venv/bin/activate
```

Windows PowerShell:

```powershell
py -m venv .venv
.venv\Scripts\Activate.ps1
```

### 3. Install dependencies

Open `notebook/RAG_Handson.ipynb` in VS Code, select the `.venv` Python kernel, and run the notebook's first installation cell:

```python
%pip install -q langchain langchain-community langchain-ollama langchain-chroma langchain-text-splitters chromadb pypdf
```

### 4. Download the local models

Make sure Ollama is running, then run:

```bash
ollama pull phi3:mini
ollama pull nomic-embed-text
```

- `phi3:mini` generates answers.
- `nomic-embed-text` embeds document chunks and questions for retrieval.

### 5. Add the PDF

Place `nasa_systems_engineering_handbook.pdf` in the repository root, next to the notebook. The notebook's `Corpus` variable points to `../nasa_systems_engineering_handbook.pdf` relative to the `notebook/` folder. If you store the PDF elsewhere, update `Corpus` to the correct path.

### 6. Run the notebook

Run cells from top to bottom:

1. Install dependencies and test the LLM.
2. Load the PDF.
3. Split the text into chunks.
4. Create embeddings and index them in ChromaDB.
5. Retrieve relevant passages.
6. Generate an answer from retrieved context.
7. Test the distance-based guardrail with covered and out-of-scope questions.

The first embedding/indexing run may take some time. The notebook stores the vector database in `./chroma_db`.

## Configuration

The notebook currently uses:

| Setting | Value | Purpose |
|---|---|---|
| `Language_Model` | `phi3:mini` | Local answer-generation model |
| `Embedding_Model` | `nomic-embed-text` | Text embedding model |
| `CHUNK_SIZE` | `800` | Target chunk size in characters |
| `CHUNK_OVERLAP` | `100` | Overlap between neighboring chunks |
| `DISTANCE_THRESHOLD` | `0.7` | Initial retrieval-distance cutoff to test and calibrate |

The meaning and scale of distance scores depend on the vector-store configuration. Do not assume `0.7` is universally correct: compare scores for questions known to be covered and not covered by the handbook.

## Troubleshooting

- **Model not found:** run the relevant `ollama pull ...` command above.
- **Connection refused:** start Ollama and retry the cell.
- **PDF not found:** check the filename and that the PDF is in the expected directory.
- **Missing Python module:** ensure the notebook uses the same `.venv` kernel where the dependencies were installed.
- **Unexpected guardrail decision:** print the best retrieval distance and test a range of questions before adjusting the threshold.

## Notes

- The PDF and generated `chroma_db/` data are not committed to this repository; add the PDF locally before running the notebook.
- Do not commit virtual environments, local secrets, or large generated data directories.
- This is a learning project intended to demonstrate a basic RAG workflow, not a production-ready factuality or safety system.

## Repository

[GitHub: abhi1207-chugh/rag-handsbook-basics](https://github.com/abhi1207-chugh/rag-handsbook-basics)
