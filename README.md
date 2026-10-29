# LangChain Parallel Chains using LCEL

[![Python](https://img.shields.io/badge/Python-3.9%2B-blue.svg)](https://www.python.org/)
[![LangChain](https://img.shields.io/badge/Framework-LangChain-green.svg)](https://www.langchain.com/)
[![Google Gemini](https://img.shields.io/badge/LLM-Google%20Gemini-4285F4.svg)](https://ai.google.dev/)
[![LCEL](https://img.shields.io/badge/Workflow-LCEL-orange.svg)](https://python.langchain.com/docs/concepts/lcel/)

A hands-on Generative AI project demonstrating **parallel execution and output aggregation using LangChain Expression Language (LCEL)**. The application uses Google Gemini and LangChain's `RunnableParallel` to generate study notes and question-answer pairs simultaneously, then combines them into a single document.

## 📌 Project Overview

In many Generative AI applications, multiple independent tasks need to be performed on the same input. Running these tasks one after another can introduce unnecessary waiting time.

LangChain's `RunnableParallel` allows independent processing branches to run concurrently. Their results can then be collected and passed to another chain for further processing.

This project demonstrates a three-stage workflow:

1. **Notes Generation:** Generate short, simple notes from the input text.
2. **Quiz Generation:** Generate five short question-answer pairs from the same text.
3. **Output Merging:** Combine the generated notes and quiz into a single document.

The example uses an explanation of **Support Vector Machines (SVMs)** as its input text.

## 🎯 Project Objectives

- Understand parallel execution in LangChain.
- Learn how to use `RunnableParallel`.
- Build workflows using LangChain Expression Language (LCEL).
- Process the same input through multiple independent LLM chains.
- Collect multiple outputs in a dictionary.
- Merge parallel outputs using a subsequent LLM chain.
- Visualize the workflow using `get_graph()`.

## 🏗️ Workflow Architecture

```text
                  Input Text
                      |
                      v
               RunnableParallel
                 /          \
                /            \
               v              v
        Notes Generation   Quiz Generation
           Prompt 1          Prompt 2
               |              |
               v              v
          Gemini Model    Gemini Model
               |              |
               v              v
          String Parser   String Parser
               |              |
               v              v
             notes           quiz
                \             /
                 \           /
                  v         v
                Output Dictionary
                       |
                       v
                   Merge Prompt
                       |
                       v
                  Gemini Model
                       |
                       v
                 String Parser
                       |
                       v
              Combined Final Document
```

## ⚙️ Technical Implementation

### 1. Google Gemini Integration

The project uses `ChatGoogleGenerativeAI` to connect LangChain with Google's Gemini model.

```python
from langchain_google_genai import ChatGoogleGenerativeAI

model1 = ChatGoogleGenerativeAI(
    model="gemini-3.1-flash-lite-preview"
)

model2 = ChatGoogleGenerativeAI(
    model="gemini-3.1-flash-lite-preview"
)
```

Two model instances are configured for the independent branches and the merging stage. The configured model is a preview model, so its availability may change.

### 2. Creating the Notes Generation Prompt

The first prompt transforms the input text into short and simple notes.

```python
from langchain_core.prompts import PromptTemplate

prompt1 = PromptTemplate(
    template="Generate short and simple notes from the following text \n {text}",
    input_variables=["text"]
)
```

The `{text}` variable receives the source content when the chain is invoked.

### 3. Creating the Quiz Generation Prompt

The second prompt generates five short question-answer pairs from the same source text.

```python
prompt2 = PromptTemplate(
    template="Generate 5 short question answers from the following text \n {text}",
    input_variables=["text"]
)
```

Both prompts receive the same input, but each performs a different task.

### 4. Using `StrOutputParser`

```python
from langchain_core.output_parsers import StrOutputParser

parser = StrOutputParser()
```

The output parser converts the model's response into a plain string that can be passed to the next component.

### 5. Executing Tasks in Parallel

The core of the project is `RunnableParallel`.

```python
from langchain_core.runnables import RunnableParallel

parallel_chain = RunnableParallel({
    "notes": prompt1 | model1 | parser,
    "quiz": prompt2 | model2 | parser
})
```

Here, two independent chains process the same input:

- The `notes` branch generates study notes.
- The `quiz` branch generates question-answer pairs.

The results are collected into a dictionary with the keys `notes` and `quiz`.

Because these branches are independent, LangChain can execute them concurrently rather than requiring the notes branch to finish before starting the quiz branch.

### 6. Merging the Results

The third prompt combines the results produced by the parallel branches.

```python
prompt3 = PromptTemplate(
    template="Merge the provided notes and quiz into a single document \n notes -> {notes} and quiz -> {quiz}",
    input_variables=["notes", "quiz"]
)
```

The merging chain is defined as:

```python
merge_chain = prompt3 | model1 | parser
```

The combined workflow is then created:

```python
chain = parallel_chain | merge_chain
```

The parallel stage produces a dictionary containing both results. LCEL passes that dictionary to the merging stage, where the `notes` and `quiz` keys supply the corresponding prompt variables.

### 7. Invoking the Complete Workflow

```python
result = chain.invoke({"text": text})
print(result)
```

The complete workflow generates notes and quiz questions concurrently, then merges them into a single final response.

### 8. Visualizing the Workflow

```python
chain.get_graph().print_ascii()
```

This displays an ASCII representation of the chain's structure, helping you understand how the parallel branches connect to the final merging stage.

## 🛠️ Tech Stack

| Technology | Purpose |
|---|---|
| Python | Main programming language |
| LangChain Core | Prompt templates, parsers, and runnable components |
| LangChain Expression Language | Composes the workflow |
| `RunnableParallel` | Runs independent processing branches concurrently |
| Google Gemini | Generates notes, quiz questions, and the merged document |
| `StrOutputParser` | Converts model responses into strings |
| `python-dotenv` | Loads environment variables from a `.env` file |

## 📂 Repository Structure

```text
langchain-parallel-chain/
│
├── parallel_chain.py   # Parallel LCEL workflow
├── README.md           # Project documentation
└── .env                # Local API key configuration (not committed)
```

## 🚀 Getting Started

### Prerequisites

- Python 3.9 or later
- A Google Gemini API key
- Internet connectivity to access the model API

### Step 1: Clone the Repository

```bash
git clone https://github.com/mani787060/langchain-parallel-chain.git
cd langchain-parallel-chain
```

### Step 2: Create a Virtual Environment

**Windows:**

```bash
python -m venv .venv
.venv\Scripts\activate
```

**macOS/Linux:**

```bash
python3 -m venv .venv
source .venv/bin/activate
```

### Step 3: Install Dependencies

```bash
python -m pip install --upgrade pip
pip install langchain-core langchain-google-genai python-dotenv
```

### Step 4: Configure the API Key

Obtain an API key through [Google AI Studio](https://aistudio.google.com/).

Create a `.env` file in the repository root:

```env
GOOGLE_API_KEY=your_gemini_api_key_here
```

Replace the placeholder with your actual API key.

**Important:** Never commit your API key or `.env` file to GitHub.

Add these entries to `.gitignore`:

```gitignore
.env
.venv/
__pycache__/
*.pyc
```

### Step 5: Run the Application

```bash
python parallel_chain.py
```

The application prints the combined notes and quiz output, followed by an ASCII visualization of the workflow.

## 💡 Example Use Case

**Input:** An educational explanation of Support Vector Machines (SVMs), including their advantages, limitations, and use cases.

**Parallel task 1 — Notes:** The model creates short, easy-to-understand study notes.

**Parallel task 2 — Quiz:** The model generates five short question-answer pairs based on the same text.

**Final output:** The model combines the notes and quiz into a single document that can be used as a study resource.

The exact wording and structure of the generated content may vary between executions.

## 🔄 Parallel Chains vs. Sequential Chains

| Sequential workflow | Parallel workflow |
|---|---|
| One stage depends on the preceding stage. | Independent branches can process the same input concurrently. |
| Useful when later steps need earlier results. | Useful when multiple tasks can be performed independently. |
| Each dependent step must wait for its required input. | Independent branches need not wait for one another to begin. |
| Example: report generation followed by summarization. | Example: notes generation and quiz generation from the same text. |

Parallel execution can reduce overall latency when independent tasks are run concurrently. Actual performance depends on model response times, API limits, network conditions, and the workload.

## 🌱 Possible Improvements

This project can be extended by:

- Accepting text from user input or a file.
- Adding exception handling for API and model errors.
- Saving notes and quiz questions to separate files.
- Returning structured quiz data using Pydantic or JSON output.
- Measuring the execution time of parallel and sequential implementations.
- Adding a third independent branch for key-term extraction.
- Building a simple interface for generating study materials interactively.

## 📚 Learning Resources

- [LangChain Documentation](https://python.langchain.com/docs/introduction/)
- [LangChain Expression Language](https://python.langchain.com/docs/concepts/lcel/)
- [Google Gemini API Documentation](https://ai.google.dev/gemini-api/docs)
- [Python Documentation](https://docs.python.org/3/)
