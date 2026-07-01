# getting-started-with-openai

A small, hands-on starter repository that demonstrates how to build simple applications with LangChain and LLMs (OpenAI and Ollama) using Python. It contains a minimal Streamlit demo app, example Jupyter notebooks that walk through initializing LLM clients and prompts, and the basic dependencies to run the examples.

## Introduction

This project is intended for developers who want a stripped-down, practical introduction to using LangChain with large language models. It shows:
- How to configure API keys and tracing with LangChain/LangSmith.
- How to create and invoke prompt chains using ChatPromptTemplate and output parsers.
- A tiny Streamlit app (app.py) that uses an Ollama model for interactive question/answering.

The code is intentionally minimal so you can read it top-to-bottom and get running quickly.

### Stack
- Language(s): Python 3.10+
- Framework / runtime: Streamlit (for the demo)
- Notable libraries: langchain, langchain-openai (adapter), langchain_community (Ollama), python-dotenv

## Repository layout

```
README.md                    Project README (this file)
app.py                       Minimal Streamlit demo using an Ollama model
requirements.txt             Python dependencies to install
GettingStarted.ipynb         Notebook with walkthroughs and example calls
1.1.2-Simpleapp.ipynb        Additional notebook example
.env                         Example environment file (kept out of repo in practice)
```

## How to run

1. Install dependencies (preferably in a virtual environment):

```bash
python -m venv .venv
source .venv/bin/activate   # on Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

2. Create a .env file in the repo root with your keys (example values):

```env
OPENAI_API_KEY=sk-...
LANGSMITH_API_KEY=ls-...
LANGCHAIN_PROJECT=your-project-name
```

3. Run the Streamlit demo (app.py):

```bash
streamlit run app.py
```

The app presents a single input box where you can type a question and see the model's response.

## Usage examples (from GettingStarted.ipynb)

Below are condensed examples taken from the notebook demonstrating environment setup, creating an LLM client, assembling a prompt chain, and invoking it.

1) Environment and LangSmith tracing

```python
import os
from dotenv import load_dotenv

load_dotenv()

# Set environment variables for tracing / LangSmith
os.environ["OPENAI_API_KEY"] = os.getenv("OPENAI_API_KEY")
os.environ["LANGSMITH_API_KEY"] = os.getenv("LANGSMITH_API_KEY")
os.environ["LANGCHAIN_TRACING"] = "true"
os.environ["LANGSMITH_ENDPOINT"] = "https://api.smith.langchain.com"
os.environ["LANGCHAIN_PROJECT"] = os.getenv("LANGCHAIN_PROJECT")
```

2) Creating a ChatOpenAI LLM (notebook example)

```python
from langchain_openai import ChatOpenAI

llm = ChatOpenAI(
    model="gpt-4.1-2025-04-14",
    temperature=0,
    timeout=20,
    max_retries=2,
)
# Use the llm in a chain rather than printing it directly
```

3) Building a chat-style prompt and invoking a chain

```python
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser

prompt = ChatPromptTemplate.from_messages([
    ("system", "You are an AI Engineer. Provide me answers based on the question."),
    ("user", "{input}"),
])

output_parser = StrOutputParser()
chain = prompt | llm | output_parser
response = chain.invoke({"input": "Explain LangChain in simple terms."})
print(response)
```

4) The Streamlit demo (app.py)

The included app uses an Ollama model (gemma3:1b) through the langchain_community Ollama wrapper. Key parts:

```python
# app.py (excerpt)
from langchain_community.llms import Ollama
import streamlit as st
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser

prompt = ChatPromptTemplate.from_messages([
    ("system","You are a helpful assistant. Please respond to the question asked"),
    ("user","Question:{question}")
])

llm = Ollama(model="gemma3:1b")
output_parser = StrOutputParser()
chain = prompt | llm | output_parser

if input_text:
    st.write(chain.invoke({"question": input_text}))
```

Notes:
- Ollama requires a local Ollama service or compatible endpoint. If you don't use Ollama, use the ChatOpenAI example above.
- The Notebook also demonstrates error traces related to LangSmith ingestion when keys/permissions are not set; you can ignore these while experimenting locally.

## Tips and next steps
- If you plan to extend this repo, consider adding a requirements-dev.txt with test tooling and CI workflows.
- Add a notebook section that shows how to swap in OpenAI vs Ollama easily (parameterize the LLM selection).

---

If you'd like, I can now commit this expanded README into the repository (I will update README.md with the above content).