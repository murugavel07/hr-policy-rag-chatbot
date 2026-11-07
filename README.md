# Nestlé HR Policy RAG Chatbot

An educational HR question-answering assistant built with **Python, LangChain, Hugging Face embeddings, ChromaDB, OpenRouter, and Gradio**.

> **Disclaimer:** This is an independent course-end assessment project, **not** an official Nestlé product. It uses the *Nestlé Human Resources Policy (September 2012)* for educational demonstration and does not represent current HR guidance.

## Features

- Extract policy content from a PDF using `PyPDFLoader`
- Split text into chunks (800-character size, 150-character overlap)
- Create 384-dimensional embeddings with `sentence-transformers/all-MiniLM-L6-v2`
- Store and search chunks in **ChromaDB**
- Retrieve relevant policy passages using semantic similarity
- Generate grounded answers through an LLM accessed via **OpenRouter**
- Show retrieved PDF page numbers in an interactive **Gradio** chatbot
- Return a fallback answer when the retrieved context lacks the requested information

## RAG architecture

```text
HR Policy PDF
   ↓ PyPDFLoader
Text cleaning and chunking
   ↓ Hugging Face MiniLM embeddings
ChromaDB vector store
   ↑ question embedding / semantic similarity
Retrieved policy excerpts
   ↓ ChatPromptTemplate + original question
OpenRouter LLM
   ↓
Gradio response + retrieved PDF pages
```

## Run in Google Colab

1. Open `Nestle_HR_RAG_Assessment_Clean.ipynb` in Google Colab.
2. Run the dependency installation cell.
3. Upload an authorized copy of the Nestlé HR Policy PDF when prompted.
4. Enter your own **OpenRouter API key** via the notebook's hidden `getpass()` input.
5. Execute the remaining cells in order, including the evaluation and Gradio launch.

The original source HR Policy PDF is intentionally **not included** in this public repository.

## Example questions

- What is Nestlé's policy on employee training?
- How does Nestlé handle employee performance?
- What does the policy say about workplace harassment and discrimination?
- How many annual paid leave days does Nestlé provide?

The last question demonstrates how the chatbot avoids inventing a numerical entitlement not stated in the supplied document.

## Technology stack

| Purpose | Technology |
| --- | --- |
| Notebook and language | Python / Google Colab |
| PDF extraction | PyPDFLoader |
| Text splitting | RecursiveCharacterTextSplitter |
| Embeddings | Hugging Face all-MiniLM-L6-v2 |
| Vector search | ChromaDB |
| LLM access | OpenRouter via LangChain ChatOpenAI |
| Prompts | LangChain ChatPromptTemplate |
| Chat UI | Gradio |

## Limitations

- The assessment brief mentions OpenAI embeddings and GPT-3.5 Turbo; this implementation substitutes Hugging Face embeddings and OpenRouter.
- `openrouter/free` can route to varying models, affecting response consistency.
- Retrieved pages are not verified claim-level citations.
- Semantic retrieval may miss relevant chunks for abbreviated or broad questions.
- A small rule-based query expansion is used for performance questions.
- Gradio accepts chat history but does not use it for conversational retrieval.
- The 2012 source policy should not be interpreted as a current HR reference.

## Security and source material

Never commit API keys, secrets, downloaded model caches, generated ChromaDB databases, or the original policy PDF without redistribution permission. Temporary Gradio share links should not be represented as permanent hosting.

## Project status

A working educational RAG prototype, developed for a course-end assessment.
