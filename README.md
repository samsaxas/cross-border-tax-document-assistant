# Cross-Border Tax Document Assistant

A GenAI assistant that answers cross-border tax questions (FBAR, FIRPTA, W-8BEN) using **Retrieval-Augmented Generation (RAG)** over public IRS publications, plus a prototype **document-review automation** workflow that extracts fields from invoices and tax forms and flags anomalies.

Built with **Python, LangChain, FAISS, and the Gemini API** in a Jupyter Notebook.

> **Disclaimer:** This is a learning/portfolio project. It is not tax or legal advice. All invoices and tax forms used in the automation demo are **synthetic**.

---

## Table of Contents
- [Overview](#overview)
- [Features](#features)
- [Architecture](#architecture)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Setup](#setup)
- [Usage](#usage)
- [Example Queries](#example-queries)
- [Document Review Automation](#document-review-automation)
- [Limitations](#limitations)
- [Future Improvements](#future-improvements)
- [Author](#author)

---

## Overview

Cross-border tax rules are dense and spread across long government publications. This project lets a user ask natural-language questions and get answers **grounded only in the source documents**, reducing hallucinations.

It has two parts:

1. **RAG Q&A assistant:** answers questions on FBAR, FIRPTA, and Form W-8BEN from public IRS publications.
2. **Document review prototype:** extracts fields from synthetic invoices and tax forms into Pandas tables and flags duplicates, missing fields, and unusual amounts.

## Features

- Semantic search over IRS publications using embeddings and a FAISS vector index
- Answer-only-from-context guardrail: the assistant declines to answer when the retrieved context does not support it
- Prompt-engineered responses using the Gemini API through LangChain
- Structured field extraction from synthetic invoices and tax forms into Pandas DataFrames
- Automated flags for duplicate records, missing fields, and unusual amounts

## Architecture

```
IRS PDFs ──► Load & Parse ──► Chunk Text ──► Embeddings ──► FAISS Index
                                                                │
User Question ──► Embed Query ──► Top-k Retrieval ◄─────────────┘
                                        │
                                        ▼
                    Prompt (question + retrieved context + guardrail)
                                        │
                                        ▼
                               Gemini API (LLM)
                                        │
                                        ▼
                          Grounded Answer / "Not found in context"
```

## Tech Stack

| Area | Tools |
|---|---|
| Language | Python |
| LLM | Gemini API |
| Orchestration | LangChain |
| Vector store | FAISS |
| Embeddings | `<embedding model used, e.g. Gemini / HuggingFace>` |
| Data handling | Pandas |
| Environment | Jupyter Notebook |

## Project Structure

```
├── <notebook-name>.ipynb     # RAG assistant + document-review automation
└── README.md
```

Everything (document loading, chunking, FAISS indexing, Q&A chain, and the automation demo) runs inside the single Jupyter Notebook.

## Setup

**1. Clone the repository**
```bash
git clone https://github.com/<your-username>/<repo-name>.git
cd <repo-name>
```

**2. Install dependencies**
```bash
pip install langchain langchain-community langchain-google-genai faiss-cpu pandas jupyter pypdf
```
> Match this list to the imports in your notebook's first cells.

**3. Add your Gemini API key**

Set it as an environment variable (never hard-code or commit it):
```bash
export GOOGLE_API_KEY=your_api_key_here     # Windows: set GOOGLE_API_KEY=your_api_key_here
```

**4. Source documents**

The notebook uses public IRS publications (FBAR, FIRPTA, W-8BEN). Add the PDFs or download links as your notebook expects.

## Usage

1. Launch Jupyter:
   ```bash
   jupyter notebook
   ```
2. Open the project notebook and run the cells in order:
   - Load and chunk the IRS documents
   - Build the FAISS index
   - Ask questions through the QA chain
   - Run the document-review automation section

## Example Queries

```text
Q: What is the FBAR filing threshold?
Q: When does FIRPTA withholding apply to a foreign seller?
Q: What is Form W-8BEN used for?
Q: What is the capital of France?   →  declined (outside the source documents)
```

The last query shows the guardrail: out-of-scope questions return a "not found in provided context" style response instead of a guess.

## Document Review Automation

A prototype that reduces manual document review on **synthetic** invoices and tax forms:

| Step | Description |
|---|---|
| Extract | Pull key fields (e.g. vendor, invoice number, date, amount) into Pandas tables |
| Flag duplicates | Detect repeated invoices |
| Flag missing fields | Highlight incomplete records |
| Flag unusual amounts | Surface outliers for human review |

Output is a review table where flagged rows can be inspected manually.

## Limitations

- Runs as a notebook, not a deployed service
- Answers are limited to the indexed IRS publications and may not reflect the latest rule changes
- Field extraction was tested on synthetic documents only, not real-world scans
- Not a substitute for professional tax advice

## Future Improvements

- Expose the assistant through a **REST API** (FastAPI) for integration with other applications
- Add a Streamlit chat interface
- Add a labeled Q&A set and evaluation metrics for answer quality
- Support OCR for scanned invoices and forms
- Add document classification (invoice vs. tax form type)

## Author

**Samriddhi Saxena**
samriddhisaxena3101@gmail.com