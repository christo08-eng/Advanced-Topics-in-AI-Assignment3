# RAG System for Cybersecurity Practitioner Guideline Generation

Assignment 3 — Advanced Topics in Artificial Intelligence
Student: Christo Joshy | Year: 2026

---

## Project Overview

This project designs, implements, and evaluates a Retrieval-Augmented Generation
(RAG) system that generates actionable cybersecurity guidelines for security
practitioners. The system retrieves relevant knowledge from the MITRE ATT&CK
Enterprise Framework and uses a quantized instruction-tuned LLM to produce
three structured guidelines per query.

---

## Repository Structure

| File | Description |
|---|---|
| final-3.ipynb | Complete final RAG pipeline (Sections 1-10) |
| intermediate_v1.ipynb | Intermediate TF-IDF prototype (Phases 1-5) showing iterative development |
| cybersecurity_kb.jsonl | 2,578-sample MITRE ATT&CK knowledge base |
| generation_results.json | Generated guidelines for all 10 practitioner queries |
| evaluation_metrics.json | RAGAS-style evaluation scores per query |
| evaluation_metrics.csv | Evaluation results in CSV format |
| final_results.csv | Combined results table |
| README.md | This file |

---

## Models Used

| Component | Model | Parameters |
|---|---|---|
| Embedding (Retrieval) | all-MiniLM-L6-v2 | 22.7M |
| Generation (LLM) | zephyr-7b-beta (4-bit NF4) | 7B |

---

## Results Summary

| Metric | Mean | Std | Min | Max |
|---|---|---|---|---|
| Context Relevance | 0.5610 | 0.0612 | 0.4915 | 0.6857 |
| Answer Relevance | 0.5217 | 0.1101 | 0.3615 | 0.6750 |
| Faithfulness | 0.4885 | 0.0770 | 0.3587 | 0.6631 |

Retrieval Hit Rate @ 3: 9/10 queries | MRR: 0.85

---

## How to Run on Kaggle

Step 1 - Create a new Kaggle notebook
Go to kaggle.com, click Create > New Notebook.

Step 2 - Upload the notebook
Click File > Import Notebook and upload final-3.ipynb.

Step 3 - Enable GPU
Go to Settings > Accelerator and select GPU T4 x2.

Step 4 - Enable Internet
Go to Settings > Internet and toggle it On.

Step 5 - Run all cells
Click Run > Run All and wait for all sections to complete.

The notebook will automatically download the MITRE ATT&CK dataset, build the
knowledge base, generate embeddings, load the LLM, run all 10 queries, and
compute evaluation metrics.

Estimated total runtime: 15-20 minutes (includes model download on first run).

---

## Pipeline Summary

Practitioner Query
        |
        v
Embedding Model (all-MiniLM-L6-v2)
        |
        v
FAISS IndexFlatIP  <---  Knowledge Base (2,578 MITRE ATT&CK chunks)
        |
        v
Top-3 Retrieved Chunks
        |
        v
Prompt Builder (ChatML format)
        |
        v
Zephyr-7B LLM (4-bit NF4 quantized)
        |
        v
3 Practitioner Guidelines
        |
        v
RAGAS-Style Evaluation
(Context Relevance | Answer Relevance | Faithfulness)

---

## Dataset

The knowledge base is sourced from the MITRE ATT&CK Enterprise Framework
(Strom et al., 2018), downloaded as a STIX 2.0 JSON file from the official
MITRE CTI GitHub repository. 697 valid techniques are each expanded into up
to 5 question-answer pairs covering:

1. What the technique is
2. Which ATT&CK tactic it belongs to
3. How defenders can detect it
4. What mitigations are recommended
5. Which platforms it affects

Final dataset: 2,578 Q&A samples | License: CC BY 4.0

---

## Key Design Decisions

| Decision | Choice | Justification |
|---|---|---|
| Knowledge source | MITRE ATT&CK | Gold-standard adversary knowledge base, CC BY 4.0 |
| Retrieval method | Dense embeddings + FAISS | Outperforms TF-IDF on vocabulary-gap queries |
| Embedding model | all-MiniLM-L6-v2 | Strong MTEB scores, fast, 22.7M parameters |
| Vector index | FAISS IndexFlatIP | Exact cosine search, negligible latency at 2,578 vectors |
| LLM | Zephyr-7B-beta | DPO instruction-tuned, MIT license, no access gating |
| Quantization | 4-bit NF4 | Reduces 14GB model to ~5GB VRAM for T4 compatibility |
| Evaluation | RAGAS-style embedding metrics | Reference-free, no API key required |

---

## References

Strom et al. (2018). MITRE ATT&CK: Design and Philosophy. MITRE Corporation.
Lewis et al. (2020). Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks. NeurIPS 2020.
Reimers & Gurevych (2019). Sentence-BERT. EMNLP 2019.
Johnson et al. (2021). Billion-scale similarity search with GPUs. IEEE Trans. Big Data.
Dettmers et al. (2023). QLoRA: Efficient Finetuning of Quantized LLMs. NeurIPS 2023.
Tunstall et al. (2023). Zephyr: Direct Distillation of LM Alignment. arXiv:2310.16944.
Es et al. (2023). RAGAS: Automated Evaluation of Retrieval Augmented Generation. arXiv:2309.15217.

