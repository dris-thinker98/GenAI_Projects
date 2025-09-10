# 📘 RAG Implementation – Exercise #1

This exercise demonstrates the step-by-step implementation of a **Retrieval-Augmented Generation (RAG)** pipeline using *ChanakyaNeeti_in_English.pdf* as the knowledge source. The pipeline combines document processing, embeddings for semantic similarity, vector storage, and a large language model for question answering.

---

## 🔹 Steps

### 1. **Load and Process PDF**
- Load the input document: `ChanakyaNeeti_in_English.pdf`.  
- Extract the text content for further processing.

---

### 2. **Text Splitting with RecursiveCharacterTextSplitter**
- Use **`RecursiveCharacterTextSplitter`** to break the document into smaller, manageable chunks.  
- **Delimiter priority order:**
  1. New paragraph  
  2. New line  
  3. Each word  
  4. Each character  
- **Configuration:**
  - Chunk size: **500 tokens**  
  - Chunk overlap: **100 tokens** (ensures no context is lost during retrieval).

---

### 3. **Convert Chunks into Embeddings**
- Use the **`all-MiniLM-L6-v2`** embedding model from SentenceTransformers.  
- Each chunk is converted into a high-dimensional vector representation.  
- These embeddings allow semantic similarity matching between user queries and document text.

---

### 4. **Store Embeddings in ChromaDB**
- Store the vector embeddings in **ChromaDB**, a vector database optimized for similarity search.  
- Each stored entry contains:  
  - Chunk ID  
  - Original chunk text  
  - Corresponding embedding  

---

### 5. **Create an LLM API Chatbot**
- Use **Hugging Face Inference Client** with the model: `meta-llama/Meta-Llama-3-8B-Instruct`.  
- The chatbot will:
  - Accept user queries.  
  - Convert queries into embeddings using the same model (`all-MiniLM-L6-v2`).  
  - Retrieve the most relevant chunks from ChromaDB based on similarity search.

---

### 6. **Retrieval and Response Generation**
- **Retrieval process:**
  1. Convert user query → vector embedding.  
  2. Search ChromaDB for top-k similar chunks (returns IDs + document text).  
- **Response process:**
  - Combine retrieved content with the user query.  
  - Pass the combined context to the LLM (`meta-llama/Meta-Llama-3-8B-Instruct`).  
  - Generate a **context-aware, prompt-adjacent answer**.

---

## 🔹 End-to-End Flow

1. Load PDF → Extract text  
2. Split text into chunks (`500` size, `100` overlap)  
3. Embed chunks with **all-MiniLM-L6-v2**  
4. Store embeddings in **ChromaDB**  
5. Accept user query → Convert query into embedding  
6. Retrieve most similar chunks from ChromaDB  
7. Feed query + retrieved content into **Meta-Llama-3-8B-Instruct**  
8. Generate final **context-grounded answer**  

---

✅ With this RAG pipeline, the LLM leverages the knowledge from *ChanakyaNeeti_in_English.pdf* while maintaining natural conversational ability.
