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

## OpenRouter API key setup (required)

This chatbot uses **OpenRouter** for text generation. You need an OpenRouter API key to run its LLM calls; a separate OpenAI API key is **not required**. The `langchain-openai` package provides the OpenAI-compatible `ChatOpenAI` client, which is pointed at OpenRouter's endpoint. There is no separate OpenRouter pip dependency in this implementation.

1. Visit [OpenRouter](https://openrouter.ai/) and sign in or create an account.
2. Open the [API Keys page](https://openrouter.ai/settings/keys).
3. Select **Create Key**, give it a recognizable name such as `nestle-hr-rag-demo`, and copy the generated key. Keep it private; if it becomes exposed, revoke it and create another.
4. In Google Colab, run the notebook's secure key-input cell and paste your key when prompted:

   ```python
   from getpass import getpass
   openrouter_api_key = getpass("Enter your OpenRouter API key: ")
   ```

5. The notebook configures LangChain to call OpenRouter:

   ```python
   from langchain_openai import ChatOpenAI

   llm = ChatOpenAI(
       model="openrouter/free",
       api_key=openrouter_api_key,
       base_url="https://openrouter.ai/api/v1",
       temperature=0,
       max_tokens=1000,
       request_timeout=60,
       max_retries=0,
       use_responses_api=False,
   )
   ```

6. Continue running the notebook. Free model availability and rate limits can vary; check the [OpenRouter models](https://openrouter.ai/models) and [documentation](https://openrouter.ai/docs) if a model is unavailable.

**Security:** Never paste your key into GitHub, a committed notebook cell, or `README.md`. `getpass()` hides input while you type and holds the key in the current Python session, so you must enter it again after restarting the runtime. Avoid sharing public Gradio links because chatbot requests can use your API key.

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
