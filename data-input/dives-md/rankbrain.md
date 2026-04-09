# RankBrain: ML Query Interpretation

## Key Insight
Pandu Nayak confirmed at trial that RankBrain is still active but 'DeepRank is taking on more and more of that capability.' RankBrain uses a feed-forward neural network (not a Transformer), making it fast and cheap enough to run on all queries. The underlying Word2Vec technology (Patent US9740680) converts words into mathematical vectors where semantic relationships become geometric relationships. 'King - Man + Woman = Queen' works as vector arithmetic. RankBrain uses this to convert novel queries like 'temperature coefficient of the opposite of night' into 'temperature coefficient of day.'

### Patent Evidence (patent)
- Patent US9740680: Computing numeric representations of words (Word2Vec) - Mikolov, Chen, Corrado, Dean. Word vectors preserving semantic/syntactic relationships
- Patent US9037464: Word2Vec (earlier filing) - Same team. Core methodology for computing word embeddings from training corpus
- Patent US9104750: Using concepts as contexts for query term substitutions - Thomas Strohmann (RankBrain co-creator). Replaces unknown terms with conceptually equivalent known terms
- Word vector dimensions encode relationships: vector('Paris') - vector('France') + vector('Italy') ≈ vector('Rome')
- RankBrain was third most important ranking factor after links and content (Google, 2015)

### API Leak Signals (leak)
- RankEmbedNearestNeighbors - Vector similarity retrieval using RankBrain-era embeddings for concept matching
- queryTermEmbedding - Per-query-term embedding vectors stored for concept substitution and nearest-neighbor lookup
- nluAnnotation - Natural language understanding annotations enabling RankBrain's concept extraction from queries
- semanticRelevanceScore - ML-derived relevance score combining RankBrain concept matching with document features
- novelQueryClassification - Classification flag for queries requiring RankBrain rewriting vs. standard keyword matching

### DOJ Trial Evidence (doj)
- Pandu Nayak: RankBrain still active, but 'DeepRank is taking on more and more of that capability'
- RankBrain handles ~15% of never-before-seen queries (original 2015 figure, confirmed at trial)
- RankBrain is computationally cheaper and faster than Transformer-based models like DeepRank
- Trained on 13 months of click data plus human rater judgments
- RankBrain and BERT/DeepRank are complementary, not replacements. Both can be active simultaneously

### How RankBrain Affects Rankings (exploit)
- Novel queries mapped to known patterns: RankBrain converts unfamiliar terms to nearest concepts
- Long-tail queries benefit most: unique phrasings resolved to established query patterns
- Concept matching > keyword matching: content covering the underlying concept ranks for related queries
- RankBrain adjusts top 20-30 document scores based on predicted query-document relevance
- Cannot 'optimize for RankBrain' directly. Comprehensive topical coverage helps concept matching
- Content answering questions in multiple phrasings aligns with how RankBrain resolves queries

### Word2Vec Embedding (Patent)
Words are converted into dense vectors in high-dimensional space (typically 300 dimensions). The training process (Skip-gram or CBOW) learns vectors where semantically similar words cluster together and relationships are preserved as vector arithmetic. RankBrain uses these embeddings to find the nearest known concept for any unknown query term.
Formula: Word2Vec(word) → vector[300d]
similarity(A, B) = cosine(vec(A), vec(B))

// Semantic arithmetic works:
vec('King') - vec('Man') + vec('Woman') ≈ vec('Queen')
vec('Paris') - vec('France') + vec('Italy') ≈ vec('Rome')
Patents: US9740680, US9037464
Affects: Affected by: training corpus size, vector dimensions, word frequency, co-occurrence patterns

### Query Concept Substitution (Patent)
RankBrain identifies concepts in the query and substitutes unknown terms with known equivalents. Rather than matching keywords, the system finds the underlying concept and maps to queries Google has seen before. The substitution considers the full query context, not just individual words. This is what enables RankBrain to handle the ~15% of never-before-seen queries.
Formula: RankBrain(novel_query) =
  extract_concepts(query_tokens)
  → find_nearest(concept, known_patterns)
  → substitute(unknown_terms, known_equivalents)
  → rewritten_query
// 'opposite of night' → 'day'
// Feed-forward NN, runs on ALL queries (cheap)
Patents: US9104750
Affects: Affected by: query novelty, concept coverage in training data, context clarity, embedding quality

### RankBrain Re-ranking Score (DOJ)
After initial retrieval and core ranking produce a candidate list, RankBrain adjusts the scores of top documents. Using the feed-forward neural network, it computes a re-ranking score based on predicted query-document relevance. This score is combined with traditional signals and other ML scores (DeepRank, RankEmbed). RankBrain is cheap enough to run for all queries, unlike DeepRank which only processes the final 20-30.
Formula: RankBrainScore(query, doc) =
  FFNN(
    embed(query),
    embed(doc_features)
  ) → rerank_adjustment
// Feed-forward NN (not Transformer)
// Runs for ALL queries (cheap + fast)
// Combined with DeepRank, RankEmbed, traditional signals
Affects: Affected by: query novelty, concept embedding quality, training data (13-month click logs + human raters)

## Pipeline
- **Query Vectorization**: Convert query terms into Word2Vec embeddings in 300-dimensional space
- **Concept Extraction**: Identify underlying concepts in the query. Unknown terms mapped to nearest known concepts
- **Query Rewriting**: Substitute novel terms with conceptually equivalent known terms for better retrieval
- **Neural Scoring**: Feed-forward neural network computes query-document relevance for top candidates
- **Score Combination**: RankBrain score combined with DeepRank, RankEmbed, and traditional signals (T*, Q*, P*)

## Timeline
- 2013: Word2Vec Published - Mikolov, Chen, Corrado, Dean publish Word2Vec. Patent US9037464 filed. Words become vectors preserving semantic relationships.
- 2015: RankBrain Launched - RankBrain deployed spring 2015. Announced October. Handles ~15% of never-seen queries. Called third most important ranking factor.
- 2018: Neural Matching Added - Neural matching launched, affecting ~30% of queries. 'Super synonym system' complements RankBrain's concept mapping.
- 2019: BERT Arrives - BERT/DeepRank launched. Complements RankBrain (not replacement). BERT understands context; RankBrain maps concepts.
- 2023: DOJ Confirms Active - Nayak testifies RankBrain still active. 'DeepRank is taking on more of that capability' but RankBrain remains for efficiency.
