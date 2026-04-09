# Neural Ranking: ML Systems in Google Search

## Key Insight
Only RankBrain and DeepRank use ML/LLM in the core ranking pipeline (HJ Kim deposition). All other signals are hand-crafted. RankEmbed BERT uses dot product similarity, NOT cosine similarity, trained on exactly 70 days of search logs combined with 16,000 human quality rater scores. The ML systems operate only on the top 20-30 results as a refinement layer.

### 6 Patents (patent)
- ML operates on top 20-30 results only (not full index)
- Hand-crafted Q*, P*, T* scores do the heavy lifting
- Word2Vec embeddings: foundational vector representations for neural ranking (US9740680, US9037464)
- LLM pairwise ranking: generative models as rankers via pairwise comparison prompting (US20250124067)
- Generative summaries for search results with interaction-based revision (US11769017)
- Candidate answer passage extraction using structured/unstructured criteria (US10180964)
- Query interpretation accuracy evaluation via temporal and cluster features (US20230334045)
- eDeepRank designed for decomposable analysis (auditability)
- MUM too computationally expensive for most queries

### Neural Signals in the API (leak)
- RankEmbedNearestNeighbors - Embedding-based retrieval matching queries to documents via vector similarity
- BertScore (PerDocData) - BERT-based relevance score stored per document, used in Ascorer signal combination
- nluAnnotation - NLU entity annotation data attached to documents for neural query-document matching
- contentTopicEmbedding - Dense topic embedding vector for neural topicality scoring beyond keyword matching
- semanticRelevanceScore - ML-derived query-document relevance score feeding into final ranking combination

### ML Signals,  Core (doj)
- RankEmbed - Dual encoder embedding queries and documents into shared vector space
- RankEmbedBERT - BERT-enhanced dual encoder with dot product similarity
- DeepRank - BERT-based neural ranking requiring language + world knowledge
- eDeepRank - Enhanced DeepRank, decomposable for engineer auditing
- RankBrain - ML system for top 20-30 docs, trained on 13 months of click data
- BERT - Bidirectional language model refining hand-crafted T* signals
- MUM - Cross-language multi-format model (too expensive for all queries)

### How ML Systems Learn (exploit)
- 70 days of search logs - Exact training window for RankEmbed BERT
- 16,000 human raters - Score IS (Information Satisfaction) 0-100
- 13 months of click data - RankBrain training window
- DotProductSimilarity - NOT cosine similarity (deliberate choice)
- RankEmbedNearestNeighbors - Video content matching via embeddings
- Human rater scores train SpamBrain, HelpfulContent, and MUM too

### RankBrain (DOJ: ML system for top 20-30 docs, 13 months click/query data + IS ratings)
ML system operating on top 20-30 documents only. Trained on 13 months of click/query data, fine-tuned on IS (human rater) scores. Interprets novel/long-tail queries using historical search patterns and unsupervised learning.
Formula: RankBrain(query, docs[20-30]) = ML_rerank(
click_logs[13_months], IS_scores[16K_raters]
)  // top 20-30 docs only, not full index
// specializes in novel/long-tail queries
Patents: US20200005149 (learning to rank using neural networks)
Affects: Affected by: content that satisfies user intent for novel queries. RankBrain helps Google understand new queries, so comprehensive topical coverage helps.

### DeepRank (DOJ: BERT-based neural network, language understanding + world knowledge)
BERT-based neural network processing language understanding and world knowledge. Can be decomposed into traditional signal equivalents (eDeepRank). Trained on user interaction data. One of only two LLM-based ranking signals.
Formula: DeepRank(query, doc) = BERT(
language_understanding, world_knowledge
)  // decomposable via eDeepRank for auditability
// 1 of only 2 LLM-based ranking signals
Patents: US20200005149 (learning to rank using neural networks)
Affects: Affected by: content quality that demonstrates genuine expertise. DeepRank "understands" content semantically, so write for humans not keywords.

### RankEmbed (DOJ: dual encoder, dot product, 70 days logs + 16K IS scores)
Dual encoder embeds queries and documents into shared vector space. Uses dot product similarity (NOT cosine, magnitude matters). Trained on 70 days of search logs + 16K human rater scores. Identifies documents beyond traditional keyword retrieval.
Formula: RankEmbed(query, doc) = dot_product(
encode(query), encode(doc)
)  // NOT cosine: magnitude matters
// trained: 70 days logs + 16K IS rater scores
Patents: US20200005149 (learning to rank)
Affects: Affected by: semantic relevance. Content can rank for queries with zero keyword overlap if semantically similar. Comprehensive, authoritative content gets "louder" embeddings.

### SigmoidCombination (DOJ: HJ Kim deposition, sigmoid functions for signal blending)
Individual ranking signals normalized and combined using sigmoid functions with threshold engineering. Core signal blending mechanism. Converts raw signal values into normalized scores before linear combination.
Formula: combined(signals) = sum(
wi * sigmoid(signal_i - threshold_i)
)  // diminishing returns past threshold
// extreme optimization of 1 signal = low marginal gain
Affects: Affected by: not directly actionable. But signals are combined non-linearly, so extreme optimization of one signal has diminishing returns. Balance across multiple signals is better.

### Word2Vec Embeddings (Patent)
Maps words to dense numeric vectors where semantically similar words are close in vector space. Two architectures: Skip-gram (US9740680) predicts context from target word; CBOW (US9037464) predicts target from context. Foundation for all neural ranking: RankEmbed, DeepRank, and content embeddings all build on Word2Vec-style representations. Tomas Mikolov (Google Brain).
Formula: Word2Vec_skipgram(w) = argmax P(context | w)
Word2Vec_cbow(w) = argmax P(w | context)
// king - man + woman ≈ queen
// foundation for RankEmbed, DeepRank, contentTopicEmbedding
Patents: US9740680 (Word2Vec Skip-gram), US9037464 (Word2Vec CBOW)
Affects: Affected by: co-occurrence patterns in training corpus. Content using natural language patterns consistent with the training distribution gets more accurate embeddings.

### LLM Pairwise Ranking (Patent)
Uses large language models as rankers by presenting document pairs and prompting the LLM to determine which is more relevant. The generative sequence model outputs ranking preferences rather than point scores. Represents the frontier of neural ranking where LLMs replace traditional scoring functions.
Formula: pairwiseRank(q, d1, d2) = LLM(
prompt: "Which document better answers query q?"
d1, d2
) → preference(d1 > d2 | d2 > d1)
// generative model as ranker, not scorer
// pairwise comparison avoids absolute calibration
Patents: US20250124067 (text ranking with pairwise ranking prompting)
Affects: Affected by: content quality at a holistic level. LLM rankers evaluate overall document utility, not individual signal optimization.

### Generative Search Summaries (Patent)
Generates LLM-powered summaries for search results that can be revised based on user interactions. The system produces initial summaries, then refines them based on which parts users engage with. Enables AI Overviews and other generative SERP features.
Formula: genSummary(results) = LLM(
top_results, query
) → summary_v1
revisedSummary = refine(
summary_v1, user_interactions
)  // interaction-revised summaries
Patents: US11769017 (generative summaries for search results)
Affects: Affected by: content clarity and structure. Well-organized content with clear claims is more likely to be accurately summarized and cited in AI Overviews.

## Pipeline
- **Hand-Crafted Scoring**: Q* + P* + T* via linear weighted formulas
- **BERT**: Query understanding, refines T* signals
- **RankEmbed**: Dual encoder, dot product similarity
- **DeepRank**: BERT-based decomposable re-ranking
- **RankBrain**: Final ML adjustment on top 20-30 results

## Timeline
- 2013: Word2Vec Published - Tomas Mikolov (Google Brain) patents Word2Vec: Skip-gram (US9740680) and CBOW (US9037464). Maps words to dense vectors where semantic relationships are preserved. Foundation for RankEmbed, DeepRank, and all neural ranking.
- 2015: RankBrain Deployed - First ML system in Google Search. Handles novel queries by learning query-document relationships from click logs.
- 2018: BERT Integration - Bidirectional contextual language model for query understanding. Refines hand-crafted T* signals.
- 2019: DeepRank System - BERT-based neural ranking requiring language understanding and world knowledge. Used for re-ranking top results.
- 2021: MUM Announced - Multitask Unified Model: cross-language, multi-format (image/text). Too computationally expensive for all queries.
- 2023: Generative Search Summaries (US11769017) - Patent on LLM-generated summaries for search results with interaction-based revision. Precursor technology for AI Overviews. Summaries refined based on user engagement patterns.
- 2024: LLM Pairwise Ranking (US20250124067) - Patent by Zhen Qin on using LLMs as rankers via pairwise comparison prompting. The generative model determines which of two documents better satisfies a query. Represents the shift from scoring functions to generative ranking.
- 2024: DOJ Trial: Training Data Revealed - RankEmbed trained on exactly 70 days of logs + 16,000 human rater scores. Uses dot product, NOT cosine similarity.
- 2024: DOJ: Only 2 Systems Use ML - HJ Kim deposition: only RankBrain and DeepRank use ML/LLM. All other signals are hand-crafted.

## Contradictions
- Public (Google Public): Our ranking is powered by advanced AI and machine learning.
  Internal (HJ Kim Deposition): Only RankBrain and DeepRank use ML/LLM. All other ranking signals are hand-crafted. 100+ raw signals combined via linear weighted formulas, not neural networks.
- Public (Google Public): BERT understands the nuance and context of every search query.
  Internal (DOJ Trial): BERT refines hand-crafted T* signals, it doesn't replace them. The ABC framework (Anchors, Body, Clicks) remains the foundation. BERT is an enhancement layer, not the core.
- Public (Google Public): MUM is 1,000x more powerful than BERT and transforms search.
  Internal (DOJ Trial): MUM is too computationally expensive for most queries. It processes cross-language and multi-format content but cannot run at the scale needed for all searches.
