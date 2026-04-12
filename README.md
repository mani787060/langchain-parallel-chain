# LangChain Parallel Chains (LCEL)
[![LangChain](https://img.shields.io/badge/Framework-LangChain-green)](https://www.langchain.com/)
[![Python](https://img.shields.io/badge/Python-3.9%2B-blue)](https://www.python.org/)
[![Gemini](https://img.shields.io/badge/Model-Google%20Gemini-4285F4)](https://ai.google.dev/)

## 🏗️ Project Overview
This repository demonstrates how to execute **Concurrent AI Tasks** using LangChain Expression Language (LCEL). In complex systems, we often need the AI to perform multiple independent analyses at once—such as checking for "Pros and Cons" or "Summarizing and Categorizing" simultaneously. This project showcases how to use **`RunnableParallel`** to branch a workflow, execute steps in parallel, and merge the results efficiently.

---

## 🛠️ Key Technical Implementations

### 1. Branching & Parallelism
* **`RunnableParallel` Integration:** Mapping a single input to multiple chains (e.g., a "Pros" chain and a "Cons" chain) that run at the same time.
* **Latency Optimization:** Demonstrating how parallel execution significantly reduces the total "Time to First Token" compared to sequential processing.

### 2. Data Aggregation
* **Merging Outputs:** Collecting the dictionaries returned by parallel branches and passing them into a final "Judge" or "Summarizer" chain for a cohesive conclusion.
* **Unified Context:** Ensuring that disparate AI thoughts are synthesized into a single, high-quality user response.

### 3. Advanced LCEL Composition
* **Non-Linear Pipelines:** Moving beyond simple vertical pipes to a "Tree" structure architecture.
* **Declarative Error Handling:** Managing how the chain behaves if one branch fails while others succeed.

---

## 💻 Tech Stack
* **Language:** Python 3.9+
* **Orchestration:** LangChain (`langchain-core`, `langchain-google-genai`)
* **Model:** Google Gemini Pro
* **Environment:** `python-dotenv`

---

## 🚀 Getting Started

1. **Clone the repository:**
   ```bash
   git clone [https://github.com/your-username/langchain-parallel-processing-lcel.git](https://github.com/your-username/langchain-parallel-processing-lcel.git)

2. **Install Dependencies:**
   ```bash
   pip install -U langchain-core langchain-google-genai python-dotenv

3. **Setup API Key:**
   Create a .env file in the root directory:

   ```bash
   GOOGLE_API_KEY=your_gemini_key_here

4. **Run the Workflow:**
   ```bash
   python parallel_chain.py
   
