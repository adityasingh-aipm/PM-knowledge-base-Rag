# 🚀 Technical Deep Dive: The PM Interview Coach (RAG)

**Author:** Aditya Singh | **Project Scope:** Local RAG Implementation & Failure Mode Audit
(https://colab.research.google.com/#scrollTo=rm8U8JCDRH77)

## 1. Executive Summary

This project implements a Retrieval-Augmented Generation (RAG) system to solve the problem of **LLM Knowledge Cutoffs** and **Hallucinations** in specialized domains (Product Management interviews). By grounding a generative model in a verified knowledge base, the system provides traceable, factually accurate coaching.

---

## 2. System Architecture

The pipeline is divided into two distinct phases: **Indexing** (offline) and **Inference** (online/query-time).

### A. Indexing Pipeline (Data Engineering)

1. **Ingestion:** Semi-structured data (PDF) is loaded using `PyPDFLoader`.
2. **Semantic Chunking:** Documents are split into **500-character blocks** with a **50-character overlap** using `RecursiveCharacterTextSplitter`.
* *PM Rationale:* This ensures context is preserved across chunk boundaries, preventing "fragmented context" failure.


3. **Vector Embedding:** Chunks are transformed into 384-dimensional dense vectors using the `all-MiniLM-L6-v2` model.
4. **Vector Storage:** Embeddings are persisted in **ChromaDB**, an in-memory vector store optimized for similarity searches.

### B. Inference Pipeline (Retrieval & Generation)

1. **Query Encoding:** The user query is converted into a vector using the same embedding model.
2. **Semantic Retrieval:** A **Cosine Similarity** search identifies the top  (where ) most relevant document chunks.
3. **Augmentation & Prompting:** Retrieved chunks are injected into a "Grounding Prompt" that restricts the LLM to the provided context.
4. **Generation:** **Gemini 3 Flash (Preview)** generates a response at `Temperature = 0` to ensure deterministic, factual output.

---

## 3. Failure Mode Analysis (The PM "Audit")

A critical part of this project was the systematic identification of RAG failure points.

| Failure Mode | Definition | Observed In-Project Behavior | PM Mitigation Strategy |
| --- | --- | --- | --- |
| **Knowledge Gap** | Query refers to data not in the corpus. | System correctly stated "I don't know" for non-PM topics. | Strict **Negative Constraints** in system prompts. |
| **Context Fragmentation** | Relevant info is split across chunks. | Initial 200-char chunks led to partial, confusing answers. | Increased chunk size to **500** with **10% overlap**. |
| **Retrieval Noise** | Top results are irrelevant (e.g., Table of Contents). | Search for "skills" returned generic page headers. | Implement **Metadata Filtering** or a **Reranker**. |
| **Model Deprecation** | API changes break model aliases. | Error 404 when calling `gemini-pro`. | Shift to **Stable Versioned Names** (e.g., `gemini-1.5-flash-002`). |
| **Citation Hallucination** | System claims support that isn't there. | AI attributed a framework to the wrong page. | Implement **Citation Scaffolding** and RAGAS evaluation. |

---

## 4. Evaluation Metrics (The "RAG Triad")

To measure success beyond "it looks right," I utilized the following framework:

* **Faithfulness (Groundedness):** Does the answer match the retrieved context? (Prevents Hallucinations).
* **Answer Relevancy:** Does the response actually address the user's specific query?.
* **Context Precision (Context Relevance):** Are the retrieved chunks truly relevant, or is there too much noise?.

<img width="1300" height="430" alt="image" src="https://github.com/user-attachments/assets/4140dcff-230a-46da-a346-718362bf3c4b" />

    AI RESPONSE:
Based on the provided context, key skills for a Product Manager include:

*   **Goal Setting:** The ability to create **SMART** goals (Specific, Measurable, Agreed upon, Realistic, and Time-bound).
*   **Strategic Alignment:** Ensuring that product goals are consistently aligned with both the overall product strategy and the broader business strategy.
*   **Prioritization:** Utilizing frameworks like **MoSCoW** (Must Have, Should Have, Could Have, Won’t Have) to categorize requirements and manage the product backlog effectively.
*   **Backlog Management:** Organizing tasks and user stories to focus on the most vital requirements that provide the highest value to users.

--- SOURCES USED ---
- pm_guide.pdf
- pm_guide.pdf
- pm_guide.pdf
- pm_guide.pdf
---
<img width="1289" height="426" alt="image" src="https://github.com/user-attachments/assets/f4a9f793-b2e4-46ae-81ec-a05b905d154c" />
--- MATCH 1 ---
manager needs to be the leader who will ensure that everyone on the team is 
working toward the same goal. 
To be able to handle such a complex and versatile role, a great product manager 
should be knowledgeable in several areas, most notably technology, business, and 
user experience. 
What Does a Product Manager Do? 
A product manager is responsible for setting a strategic plan for product creation and 
making sure that the plan is executed. In other words, a product manager is in


--- MATCH 2 ---
manager needs to be the leader who will ensure that everyone on the team is 
working toward the same goal. 
To be able to handle such a complex and versatile role, a great product manager 
should be knowledgeable in several areas, most notably technology, business, and 
user experience. 
What Does a Product Manager Do? 
A product manager is responsible for setting a strategic plan for product creation and 
making sure that the plan is executed. In other words, a product manager is in


--- MATCH 3 ---
8 
 
  
 
Roles in Product Management 
The concept of product management includes many different roles such as chief 
product officer, director of product management, product manager, product owner, 
and product marketing manager, to name just a few. 
It’s important to keep in mind that product management and project management 
are two different roles.  
There can be one person or a whole team in charge of product management. The


--- MATCH 4 ---
8 
 
  
 
Roles in Product Management 
The concept of product management includes many different roles such as chief 
product officer, director of product management, product manager, product owner, 
and product marketing manager, to name just a few. 
It’s important to keep in mind that product management and project management 
are two different roles.  
There can be one person or a whole team in charge of product management. The


--- MATCH 5 ---
4 
 
  
Introduction To Product Management 
Product management is the practice of planning, developing, marketing, and 
continuous improvement of a company’s product or products. 
The idea of product management first appeared in the early 30s with a memo 
written by the president of Procter & Gamble, Neil H. McElroy, where he introduced 
the idea of a product manager — a “brand man” completely responsible for a brand 
and instrumental to its growth.

<img width="1306" height="333" alt="image" src="https://github.com/user-attachments/assets/88fa3c8c-6863-4ce6-9021-0c3076aba560" />
A product strategy is the foundation of the entire product lifecycle. It is a high-level plan that provides direction to the product manager and the team, outlining the steps necessary to turn a product into a success. It bridges the gap between development, marketing, sales, and support by answering three core questions:
1.  **What** are you building?
2.  **Why** are you building it?
3.  **Who** are you building it for?

### How to Create a Product Strategy
To create an effective product strategy, you should define the following key components:

*   **Product Vision:** Determine the "why" behind the product. This should capture the essence of what you want to accomplish and how the product will improve the lives of its users.
*   **Target Market/Customer Persona:** Identify the "who" by defining exactly who will be using the product.
*   **SMART Goals:** Set specific, measurable, achievable, relevant, and time-bound objectives that define what success looks like.
*   **Initiatives:** Define the high-level efforts and strategic themes necessary to achieve the goals you have set.

A successful strategy should be visible to all stakeholders and serve as a guide for both inbound and outbound product management throughout the product creation process.

```markdown
# The Iterative Optimization Journey

The evaluation was not a one-time event but a series of **"Product Sprints"** to fix specific failure modes:

## Phase 1 (The Naive Baseline)
- **Approach:** Standard chunking (**500 chars**).
- **Result:** Low accuracy. Information was fragmented across chunks, leading to incomplete answers.

## Phase 2 (The Data Architecture Pivot)
- **Approach:** Increased chunk size to **1000 chars** and overlap to **100**.
- **Result:** Context Precision jumped to **0.8750**, proving the retriever was now finding the **"whole"** answer.

## Phase 3 (The Prompt Guardrail)
- **Approach:** Implemented strict **"negative constraints"** (e.g., *"Do not use outside knowledge"*).
- **Result:** Faithfulness increased to **0.72**, significantly reducing hallucinations compared to the baseline.

---

# Final Evaluation Results

The final audit was conducted using the **Ragas (RAG Assessment) framework**.

## Final Metric Table

| Metric             | Score  | Industry Benchmark | Interpretation & PM Action |
|-------------------|--------|-------------------|----------------------------|
| Context Precision | 0.8750 | > 0.80            | **Excellent.** The retriever consistently places the most relevant information at the top of the results. |
| Faithfulness      | 0.7231 | > 0.90            | **Moderate.** While improved, 28% of statements still lack direct support. **Action:** Implement Few-Shot Prompting. |
| Answer Relevancy  | 0.6812 | > 0.80            | **Good.** The answers are on-topic but could be more concise. **Action:** Optimize for brevity and directness. |

---

# Final PM Interpretation

## The Successes
The system's strongest asset is its **Retrieval Accuracy**. With a Precision of **0.875**, the **"Inventory Management"** (chunking and embedding) is production-ready. The system successfully handles complex queries about PM frameworks like **SMART goals** and **MoSCoW**.

## The Challenges (Roadmap for V2.0)
The current bottleneck is **Groundedness**. A Faithfulness score of **0.72** indicates the model occasionally relies on its own training data rather than the provided PDF.

### Proposed Solution
Transition from a simple prompt to a **"Self-RAG"** architecture where the model is asked to cite specific **page numbers for every claim** it makes. This would provide the transparency required for high-stakes interview coaching.

# The Conclusion
The project successfully demonstrates a functional **RAG pipeline** that prioritizes **source-truth over model-creativity**. It is a robust prototype that solves the **"lost-in-the-middle"** problem and effectively manages semi-structured PDF data.
```

## 5. Future Roadmap

* **Hybrid Search:** Combine keyword (BM25) and vector search to improve retrieval of specific terms like "MoSCoW".
* **Agentic RAG:** Use an agent to decompose multi-part questions into sub-queries for better synthesis.

