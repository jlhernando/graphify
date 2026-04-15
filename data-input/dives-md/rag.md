# RAG: Retrieval-Augmented Generation

## Key Insight
RAG is the most actionable influence vector for LLM outputs. Unlike training data (fixed at training time), RAG retrieves content at query time, meaning your content's current state determines whether it's cited. The RAG pipeline has three stages: (1) Embedding: your content is converted to vectors, (2) Retrieval: vectors similar to the user's query are found, (3) Generation: the LLM writes a response citing retrieved content. To be cited, your content must: be in the retrieval corpus, be well-embedded (clear, structured, on-topic), and rank high in vector similarity to the query. This is fundamentally different from traditional SEO: LLM citation depends on embedding similarity, not link authority.

### RAG Architecture (patent)
- Core concept (Lewis et al., 2020): Retrieve relevant documents from external knowledge base, concatenate with query in context window, generate response grounded in retrieved content. Reduces hallucination by providing factual grounding
- Embedding models: Text converted to dense vectors (768-3072 dimensions). Models: OpenAI ada-002/text-embedding-3, Cohere embed-v3, BGE, E5, Nomic. Cosine similarity measures relevance between query and document vectors
- Vector databases: Store and search billions of vectors. Pinecone (cloud-native), Weaviate (open-source), ChromaDB (lightweight), Qdrant, Milvus, pgvector (PostgreSQL). Approximate Nearest Neighbor (ANN) algorithms: HNSW, IVF, ScaNN
- Chunking strategies: Documents split into chunks (256-2048 tokens) before embedding. Fixed-size, sentence-level, semantic, recursive splitting. Chunk boundaries affect retrieval quality. Too small = missing context. Too large = diluted relevance
- Reranking: Initial vector retrieval returns top-K candidates (K=10-50). Cross-encoder reranker (Cohere Rerank, BGE Reranker, ColBERT) re-scores for precision. Reranking dramatically improves answer quality at moderate compute cost

### RAG in Production Systems (leak)
- Google AI Overviews: Uses FastSearch (simplified retrieval with RankEmbed, no NavBoost). AI Overview citations correlate with top-3 organic results. RAG grounds Gemini's response in search results
- Perplexity: Full RAG pipeline with web search retrieval. Cites sources inline. Uses Bing/Google APIs for retrieval, then LLM for synthesis. Transparent citation model
- Bing Chat / Copilot: RAG over Bing search index. Retrieves web results, generates conversational response with citations
- ChatGPT with browsing: When enabled, searches the web and injects results into context. RAG over live web content
- Enterprise RAG: Companies build RAG over internal documents (Notion, Confluence, Slack). LangChain, LlamaIndex are dominant frameworks. The retrieval corpus is the company's knowledge base

### How to Be Retrieved and Cited by RAG Systems (exploit)
- Be in the retrieval corpus: For web-based RAG (Perplexity, AI Overviews), your content must rank in search results. For enterprise RAG, your content must be in the document store. The first requirement is visibility in the retrieval source
- Optimize for embedding similarity: Embedding models measure semantic similarity, not keyword matching. Content should clearly, directly address the topic. Use natural language that matches how users phrase questions. Avoid jargon-heavy text that distances you from query phrasing
- Structure content for chunking: Clear headings, self-contained sections, FAQ format. Each section should be independently comprehensible when extracted as a chunk. If your key information spans multiple sections, it may be split across chunks and lost
- Answer questions directly: RAG retrieves content that answers the query. Start paragraphs with direct answers. Use 'What is X? X is...' patterns. Position-zero SEO tactics align with RAG retrieval
- Provide unique, authoritative information: If multiple sources say the same thing, the reranker favors the most authoritative/comprehensive source. Original data, unique insights, and expert analysis get selected over generic rewrites
- Maintain freshness: RAG retrieves current content. Unlike training data (frozen), RAG benefits from regularly updated content. Keep information current for ongoing citation

### Calculations
- Vector Similarity Retrieval: Query text is embedded into a vector. Cosine similarity (or dot product) computed against all document chunk vectors in the database. Top-K most similar chunks retrieved. ANN algorithms (HNSW, IVF) make this sub-linear time even for billions of vectors. Affects: Determines which content is retrieved. Embedding quality and chunking strategy directly impact retrieval accuracy.
- RAG Generation Pipeline: Retrieved chunks are concatenated with the user query in the LLM's context window. The system prompt instructs the LLM to ground its response in the retrieved content and cite sources. The LLM generates a response that synthesizes information from multiple retrieved chunks. Affects: Content position in context matters. First retrieved chunks get more attention. Clear, direct content is more likely to be cited in the response.

### RAG Pipeline
- Document Ingestion: Source documents collected from web, databases, or document stores. Split into chunks (256-2048 tokens) with metadata (URL, title, date).
- Embedding: Each chunk converted to dense vector (768-3072 dims) using embedding model (ada-002, Cohere embed, BGE). Vectors stored in vector database with metadata.
- Query Embedding: User query converted to vector using same embedding model. Ensures query and documents are in the same vector space.
- Retrieval: ANN search finds top-K most similar chunks. Typical K=10-50. Sub-millisecond search over billions of vectors via HNSW/IVF.
- Reranking: Cross-encoder reranker re-scores top-K for precision. Reduces to top-3-5 most relevant chunks. Significantly improves answer quality.
- Context Assembly: System prompt + retrieved chunks + user query assembled in LLM context window. Chunk order matters (most relevant first).
- Generation: LLM generates response grounded in retrieved content. Cites sources from chunk metadata. Synthesis across multiple chunks.

### Timeline
- 2020 (patent): RAG Paper (Lewis et al.) - Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks. Combines retrieval with generation for factual grounding.
- 2022 (event): LangChain + LlamaIndex - Frameworks for building RAG applications become available. Enterprise RAG adoption begins.
- 2023 (event): Bing Chat / Perplexity Launch - Consumer RAG products launch. Web search + LLM generation with citations. RAG becomes user-facing.
- 2023 (event): Vector DB Boom - Pinecone ($750M raise), Weaviate, ChromaDB, Qdrant emerge as dedicated vector database companies.
- 2024 (event): Google AI Overviews - Google deploys RAG at scale via FastSearch + Gemini. AI Overviews use simplified retrieval (RankEmbed, no NavBoost) to ground responses in search results.
- 2025 (event): RAG as Default LLM Pattern - Most LLM applications use RAG. Agentic RAG (multi-step retrieval + tool use) becomes standard for complex tasks.

### Contradictions
- Public (AI companies (knowledge claims)): Our AI has been trained on vast amounts of data and has extensive knowledge across all domains. vs Internal (Training data cutoff + hallucination rates): LLM training data has a cutoff date (months to years old). Without RAG, models confabulate (hallucinate) current information. RAG exists precisely because training data knowledge is insufficient: it's stale, incomplete, and unreliable for factual queries. The 'extensive knowledge' claim obscures that much of what models state confidently is pattern-matched guessing.
- Public (AI search products (citation claims)): Our AI cites its sources and provides reliable, grounded information from the web. vs Internal (RAG retrieval limitations + citation accuracy): RAG retrieval depends on embedding similarity, not factual accuracy. A well-written but incorrect source can be retrieved and cited over a poorly formatted but correct one. Citation does not mean verification. The LLM may also misrepresent the retrieved content, citing a source for claims it doesn't actually make.

### Overview
RAG (Lewis et al., 2020) augments LLMs with external knowledge by retrieving relevant documents at query time and injecting them into the context window. Instead of relying solely on training data knowledge (which becomes stale), RAG retrieves current information from a vector database, web search, or document store. The LLM then generates a response grounded in the retrieved content. Google's AI Overviews use RAG via FastSearch (simplified retrieval using RankEmbed, no NavBoost). Perplexity, Bing Chat, and most AI search products use RAG. Being in the retrieval corpus is the most direct path to being cited by an LLM.
