# DeepRank: BERT-Based Neural Ranking

## Key Insight
DeepRank is one of only two ML-based ranking systems (alongside RankBrain), per Pandu Nayak's testimony. It uses BERT and transformers requiring both language understanding and world knowledge. The enhanced eDeepRank is uniquely decomposable: engineers can audit exactly why the LLM ranks a page at a given position, breaking neural rankings into traditional signal equivalents. This decomposability is unique in ML ranking, designed for accountability.

### Signals,  Core (doj)
- DeepRank - BERT-based neural ranking requiring language + world knowledge (core)
- eDeepRank - Enhanced version with decomposable analysis capability
- eDeepRankDecomposable - Engineers can audit why LLM ranks pages (HJ Kim testimony)
- Processing limit: top 20-30 only - Both RankBrain and DeepRank are too computationally expensive to scale beyond the top 20-30 ranked documents (Nayak testimony). NavBoost and core ranking signals handle the broad candidate set (tens of thousands). This is why DeepRank is a refinement stage, not a retrieval or broad ranking signal
- One of only two ML systems in ranking (with RankBrain)
- Can decompose into traditional signal equivalents
- Trained on user data (click patterns and engagement)
- Nayak (PXR0357): DeepRank signals can be decomposed into 'signals that resembled traditional signals.' Combining neural + traditional signals improved results beyond either alone
- Pre-DeepRank context (UPX0197, Lehman 2019): Before BERT/DeepRank, Google relied on 'memorization systems much larger than any current ML model.' DeepRank represents the shift from memorization-based ranking to language understanding
- Generalization system (Mehta FOF 97): DeepRank is classified as a 'generalization' system alongside RankBrain, RankEmbed, RankBERT, and MUM. These systems are 'designed to fill holes in [click] data' by generalizing from data-rich to data-sparse situations. Older signals use up to 1 trillion examples; DeepRank requires only ~1 billion (FOF 98). DeepRank 'not only gives significant relevance gains but ties ranking more tightly to language understanding'
- Behavioral embedding paradigm (UPX0205, Research All-Hands Apr 2015): Pre-DeepRank concept: 'Instead of describing what each result is about, describe it based on the people who took it.' Users described by past actions, not demographics. 'Behavioral similarity' matching. At Google's scale, 'even the most obscure choice would have been exercised by thousands of people.' This is the conceptual foundation for embedding users and documents in shared behavioral space

### eDeepRank Transparency Design (PXR0356, HJ Kim Feb 2025) (doj)
- eDeepRank is an LLM system using BERT/transformers that decomposes LLM-based signals into components to make them more transparent
- Almost every signal aside from RankBrain and DeepRank is hand-crafted. These two are the only LLM-based ranking signals
- Hand-crafting advantage: 'if anything breaks Google knows what to fix. Google wants their signals to be fully transparent'
- Microsoft contrast: 'Microsoft builds very complex systems using ML techniques to optimize functions. So it's hard to fix things.' Google deliberately avoids this approach for ranking

### Lehman's Deep ML Prediction (UPX0197, Grushetsky Email Jan 2019) (doj)
- Lehman prediction: 'Within the near future, a deep ML system will clearly outperform Google's 20-year accumulation of relevance algorithms'
- BERT subsumed past work: 'deep ML (in the form of BERT) abruptly subsumed essentially all preceding work' in web answers
- Unsupervised learning replacing feedback: 'huge amounts of user feedback can be largely replaced by unsupervised learning from raw text'
- Pre-DeepRank memorization: 'we rely on memorization systems that are much larger than any current ML model'
- Grushetsky response: need to 'deconstruct the various reasons why we often prefer our own artisanal ranking systems over an essentially end-to-end deep system'
- Concerns raised: predictability of changes, anticipating errors, working at scale, avoiding over-reliance on metrics, team morale

### Neural Ranking Signals (leak)
- mustangRankEmbedInfo - Deep learning embedding vectors in Mustang
- compressedDocEmbedding - Compressed document embeddings for retrieval
- pageEmbeddings - Page-level semantic vector encoding
- STS - Semantic Text Scores, aggregate understanding
- rankEmbedInfo - RankEmbed integration for neural scoring
- DeepRank and RankEmbed share the neural ranking infrastructure

### 5 Patents (patent)
- Transformer attention: the "Attention Is All You Need" sequence transduction mechanism underlying BERT and DeepRank (US10452978)
- RAG response generation: retrieval-augmented generation combining search results with LLM output (US20240346256)
- Activity log training: using user activity logs to train semantic matching models for ranking (US20230267277)
- Knowledge panel triggering: topicality scoring to determine when to show knowledge panels (US10922326)
- Media consumption history: personalizing knowledge cards using media consumption patterns (US20240037137)

### How DeepRank Affects Your SEO (exploit)
- Write for semantic relevance, not keyword density: DeepRank uses BERT cross-attention between query and document representations. It understands meaning, not just term matching. Content that naturally addresses the topic with contextually appropriate language scores better than keyword-stuffed pages. Focus on answering the query's intent comprehensively.
- Demonstrate world knowledge in your content: The DOJ confirmed DeepRank requires both language understanding AND world knowledge. Pages that demonstrate factual depth, reference related concepts, and show contextual understanding of the topic score higher than surface-level content. Include specific details, examples, and connections between concepts.
- Optimize for the decomposable signals eDeepRank maps to: eDeepRank breaks neural scores into traditional signal equivalents: content relevance, authority, freshness, and engagement. This means improving any of these traditional signals also improves your DeepRank score. The neural model is not a black box; it reflects and amplifies the same signals Google has always used.
- Generate positive user engagement signals to retrain DeepRank: DeepRank is trained on user click data and engagement patterns. Its relevance scoring degrades without fresh click signals. Creating content that earns satisfied clicks (long dwell time, low bounce rate) provides the training data that reinforces your rankings in the next model update.
- Target semantic gaps between query and existing results: DeepRank's BERT encoding creates embedding vectors in a shared space. When existing top results don't fully address query intent (partial semantic matches), there's an opportunity. Create content that is a closer semantic match to the full query intent, and DeepRank's cross-attention mechanism will reward it.
- Build comprehensive document embeddings through content depth: compressedDocEmbedding and pageEmbeddings encode your page as a dense vector. Thin, shallow content produces weak embedding signals. Deep, comprehensive content creates richer vectors that can match a wider range of related queries in the neural scoring pipeline.

### DeepRank (DOJ)
BERT-based neural ranking system requiring both language understanding and world knowledge. One of only 2 ML systems in ranking (with RankBrain), per Pandu Nayak testimony. Uses transformer cross-attention between query and document representations to produce relevance scores.
Formula: DeepRank(q, d) = BERT_crossattn(
query_repr, doc_repr
) → relevance_score
// requires language understanding + world knowledge
// 1 of only 2 ML systems in ranking (Nayak testimony)
Patents: US20200005149 (learning to rank), US10963505 (neural ranking), US10452978 (Transformer attention), US20230267277 (activity log training for semantic matching)
Affects: Affected by: query-document semantic alignment, BERT training data, user click patterns, world knowledge embeddings

### eDeepRank (DOJ)
Enhanced DeepRank designed to be decomposable. Engineers can audit exactly why the LLM ranks a page at a given position. Breaks down neural relevance scores into interpretable components that map to traditional signal equivalents.
Formula: eDeepRank(q, d) = DeepRank(q, d) + decompose(
relevance, authority, freshness, ...
)  // maps neural score to traditional signals
// engineers can audit why LLM ranks a page
Patents: US20200005149 (learning to rank), US10963505 (neural ranking)
Affects: Affected by: same inputs as DeepRank, plus decomposition architecture for transparency

### eDeepRankDecomposable (DOJ)
Breaks neural ranking scores into traditional signal equivalents for engineering audits. HJ Kim testimony confirmed this capability. Allows teams to understand which aspects of a page (content relevance, authority, freshness) drive the neural score.
Formula: Decompose(neural_score) = {
content_relevance: w1, authority: w2,
freshness: w3, engagement: w4
}  // traditional signal equivalents
// confirmed by HJ Kim testimony
Patents: US10963505 (neural ranking)
Affects: Affected by: neural model architecture, signal decomposition layer, traditional signal mappings

### compressedDocEmbedding (Leak)
Compressed document vector representations used in the neural scoring pipeline. Documents encoded into dense vectors for efficient similarity computation and retrieval. Exact embedding dimensions and compression method not confirmed.
Formula: DocEmbed(doc) = compress(
BERT_encode(doc_content)
) → dense_vector[d]
// similarity = dot_product(query_vec, doc_vec)
// compression reduces storage for 400B+ docs
Patents: US20200005149 (learning to rank)
Affects: Affected by: document content, embedding model version, compression algorithm, vector dimensionality

### Transformer Attention (Patent)
The core mechanism behind DeepRank. Multi-head self-attention allows the model to attend to different positions in the input sequence simultaneously. Query, key, and value projections enable the model to learn which parts of a document are relevant to which parts of a query. The Vaswani et al. architecture ("Attention Is All You Need") replaced recurrence with pure attention, enabling parallel training at scale.
Formula: Attention(Q, K, V) = softmax(
Q × KT / √d_k
) × V
MultiHead = concat(
head_1, ..., head_h
) × WO
// foundation for BERT, DeepRank, RankEmbed
Patents: US10452978 (Transformer attention, Vaswani et al.)
Affects: Affected by: input sequence length, attention head count, model dimensionality. DeepRank's cross-attention between query and document builds directly on this architecture.

### RAG for Search (Patent)
Retrieval-Augmented Generation combines traditional search retrieval with LLM generation. The system retrieves relevant documents via the ranking pipeline, then feeds them as context to a generative model that produces responses. This is the architecture behind AI Overviews: search results as grounding context for LLM output. Nayak (PXR0357) confirmed FastSearch (RankEmbed-only) is used as RAG for the Gemini app.
Formula: RAG(query) = LLM(
query, retrieve(query, index)
) → grounded_response
// retrieve → rank → generate with context
// FastSearch (RankEmbed-only) used as RAG for Gemini
Patents: US20240346256 (RAG response generation)
Affects: Affected by: retrieval quality determines generation quality. Content that ranks well in RankEmbed is more likely to be cited in AI Overviews and Gemini responses.

## Pipeline
- **BERT Encoding**: Bidirectional transformer encodes query + document
- **Cross-Attention**: Query-document interaction via transformer layers
- **Neural Score**: Relevance prediction from language + world knowledge
- **Decomposition**: eDeepRank breaks score into auditable signal equivalents
- **Score Fusion**: Merged with Mustang, NavBoost, and other signals

## Timeline
- 2015: RankBrain Launches - First ML ranking signal. Processes only top 20-30 documents (too expensive to scale further). Trained on 13 months of click/query data, cross-language and cross-locale. Fine-tuned using IS rating data. Specializes in long-tail and novel queries. Uses unsupervised learning. One of only two ML systems in ranking.
- 2017: Transformer: Attention Is All You Need (US10452978) - Vaswani et al. patent the Transformer architecture. Multi-head self-attention replaces recurrence, enabling parallel training at scale. Foundation for BERT, DeepRank, RankEmbed, MUM, and all modern neural ranking.
- 2018: BERT Arrives - Bidirectional transformers revolutionize NLP. Google begins integrating BERT into search ranking.
- 2019: DeepRank Deployed - BERT-based neural ranking system requiring both language understanding and world knowledge.
- 2021: eDeepRank - Enhanced DeepRank designed for decomposable analysis. Engineers can audit exactly why pages rank.
- 2024: DOJ Trial: LLM Ranking - Pandu Nayak testified DeepRank uses BERT/transformers. Only two systems use ML: RankBrain and DeepRank.
- 2024: DOJ: Decomposable Audit - HJ Kim testified eDeepRank is decomposable: engineers can audit exactly why the LLM ranks a page.
- 2025: DOJ Remedies: eDeepRank Transparency (PXR0356) - HJ Kim interview (Feb 2025): eDeepRank uses BERT/transformers to decompose LLM signals into transparent components. Almost every signal except RankBrain and DeepRank is hand-crafted. Google deliberately chooses transparency over complex ML: 'if anything breaks Google knows what to fix.' Contrasts with Microsoft's approach of complex ML optimization that is 'hard to fix.'
- 2019: Lehman's Deep ML Prediction (UPX0197) - Eric Lehman predicted 'a deep ML system will clearly outperform Google's 20-year accumulation of relevance algorithms.' BERT 'abruptly subsumed essentially all preceding work' in web answers. Grushetsky responded with concerns about predictability, scale, metrics over-reliance, and team morale.
- 2024: RAG for Search (US20240346256) - Patent on retrieval-augmented generation for search: combining ranked results as context for LLM response generation. Architecture behind AI Overviews. Nayak confirmed FastSearch (RankEmbed-only) used as RAG for Gemini app.
- 2025: DOJ Remedies: Neural + Traditional Combination (PXR0357) - Nayak interview (Jan 2025): BERT-based DeepRank ML signals can be decomposed into signals resembling traditional signals. Combining traditional and ML-predicted signals produced better outcomes than either alone.

## Contradictions
- Public (Google Public): "AI is deeply integrated throughout our search ranking."
  Internal (DOJ Trial): Only TWO systems use ML in ranking: RankBrain and DeepRank. NavBoost is a table lookup. Q* is hand-crafted. The vast majority of Google's ranking is NOT AI/ML-based, despite marketing claims.
- Public (Google Public): "Our neural models are black boxes that we can't fully explain."
  Internal (DOJ Trial): eDeepRank is specifically designed to be decomposable. Engineers CAN audit exactly why the LLM ranks a page. Google built explainability into DeepRank, contradicting the "black box" narrative used to avoid transparency.
- Public (Google Public): "BERT understands content like a human would."
  Internal (DOJ Trial): DeepRank requires retraining on user data. Its "understanding" degrades without fresh click signals. The system learns statistical patterns from user behavior, not genuine comprehension.
- Public (Google Public): "Google Search is powered by sophisticated machine learning throughout the ranking process."
  Internal (DOJ Remedies PXR0356 (HJ Kim, Feb 2025) + UPX0197 (Grushetsky, Jan 2019)): Almost every ranking signal aside from RankBrain and DeepRank is hand-crafted. Google deliberately chose 'artisanal' hand-tuned systems over end-to-end ML because 'if anything breaks Google knows what to fix.' Internally, Google contrasted itself with Microsoft, whose complex ML systems are 'hard to fix.'
