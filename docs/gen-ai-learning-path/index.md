# Generative AI Learning Path

This document outlines a structured learning path for mastering Generative AI, covering essential concepts, tools, and techniques. The path is divided into beginner, intermediate, and advanced levels, with each section building on the previous one.

## Pre-requisites

Before diving into Generative AI, ensure you have a understanding of the following concepts:

- Python and `pip` package management
- Google Colab or Jupyter Notebooks

## Beginner Level

- Familiarity with machine learning concepts like training, testing, and evaluation metrics.
- Understanding of how neural networks work.
- Natural Language Processing (NLP) fundamentals.
- Using Hugging Face Transformers library.
- Tokenization and text preprocessing techniques.
- Understanding of vectors and similarity measures.
- Understanding of word embeddings.

### Resources

#### Free LLM APIS

- [Together AI](https://www.together.ai/)
    - Go to models page and search for free models.
    - You also get $5 free credits to use on their platform.
  
- [Groq](https://console.groq.com/home)
    - Offers some level of free usage.  

- [Google Gemini](https://aistudio.google.com/apikey)
    - Offers free access to some of their Gemini models.

- If you have good GPU on you local machine, you can use [Llama.cpp](https://github.com/ggerganov/llama.cpp).
    - Its good for prototyping and testing LLM code locally but do not expect high performance.

#### Courses

- [Daily Dose of Data Science](https://www.dailydoseofds.com/) I highly recommend this resource for practical AI ML skills. This is a paid resource but worth the investment.
- [https://developers.google.com/machine-learning/crash-course](https://developers.google.com/machine-learning/crash-course)

### Projects

1. **Text Classification with Transformers**
   - Build a text classification model using Hugging Face Transformers for sentiment analysis.
   - Try experimenting with different pre-trained models.

1. **Experiment with LLM APIs**
    - Use free LLM APIs to generate text based on prompts.
    - Compare the outputs of different models.
    - Experiment with different prompt structures to see how they affect the generated text.

## Intermediate Level

- Understanding of LLMs (Large Language Models)
- Prompt engineering techniques.
- RAG (Retrieval-Augmented Generation) concepts.
- Vector databases and their applications (FAISS, Pinecone, Qdrant).
- Text extraction from various formats (PDF, DOCX, Image) using `pdfplumber`, `pytesseract`.
- LangChain framework.

### Resources

#### Courses

- [LLM Course by Hugging Face](https://huggingface.co/learn/llm-course/chapter1/1)
- [LangChain Mastery in 2025 | Full 5 Hour Course LangChain v0.3](https://www.youtube.com/watch?v=Cyv-dgv80kE&ab_channel=JamesBriggs)

#### Vector DBs

- [Pinecone](https://www.pinecone.io/start/)
    - Free tier available with limited usage.
- [Qdrant](https://qdrant.tech/)
    - Docker image available for local setup.
- [FAISS](https://faiss.ai/)
    - Available as a library for local use.

### Projects

1. **RAG Implementation**
    - Implement a RAG system using a combination of retrieval and generation techniques.

1. **Document Summarization**
    - Develop a document summarization tool using LLMs.

1. **Vector Database Exploration**
    - Explore the use of vector databases for storing and retrieving embeddings.
    - Implement a simple application that uses a vector database for semantic search.

## Advanced Level

- Memory Management in LangChain.
- LangGraph for building complex workflows using agents.
- Model Context Protocol.
- OCR (Optical Character Recognition) techniques.
- Advanced chunking techniques for LLMs.
- Pytorch Deep Learning framework.
- Fine tuning LLMs using PEFT.

### Resources

- [Enterprise AI Tutorial – Embeddings, RAG, and Multimodal Agents Using Amazon Nova and Bedrock](https://www.youtube.com/watch?v=HaUe2AN210g&t=6184s&ab_channel=freeCodeCamp.org) - Watch at 2x speed
- [Agentic AI With LangGraph](https://www.youtube.com/playlist?list=PLZoTAELRMXVPFd7JdvB-rnTb_5V26NYNO)
- [Introduction to LangGraph](https://academy.langchain.com/collections)

### Projects

1. **Advanced RAG System**
    - Build a sophisticated RAG system that integrates multiple data sources and advanced retrieval techniques.
    - Implement advanced chunking techniques to optimize the retrieval process.
    - Integrate Vision capabilities using OCR for processing images and PDFs.
  
1. **Custom LLM Fine-tuning**
    - Try fine-tuning a very small LLM using PEFT(Lora, QLoRA)
    - Fine-tune a pre-trained LLM on a specific domain or dataset.

1. **LangGraph Workflow**
    - Create a workflow using LangGraph that involves multiple agents and tasks.
    - Supervisor Agents and Hierarchical Agents.
  
1. **Multi-modal Agent**
    - Use text + image input (e.g., invoice parser or medical report assistant).

## Deployment & Production Concepts

- Understanding of containerization (Docker).
- Familiarity with at least one cloud platform (AWS, GCP, Azure) for deploying models.
- Familiarity with Backend frameworks (Flask, FastAPI) for building APIs.
- Knowledge of CI/CD pipelines for model deployment.
- Monitoring and logging practices for production systems.
