# Passage Ranking: Section-Level Scoring

## Key Insight
Passage Ranking changed the fundamental unit of ranking from whole pages to sections within pages. Google clarified that pages are still indexed as whole documents, but passages are scored as an additional ranking factor. This means a 5,000-word article with one highly relevant paragraph can now rank for specific queries. BERT powers the language understanding that enables passage identification and scoring.

### 12 Patents (patent)
- Patent US20160078102: Text indexing and passage retrieval - annotated passage indexes
- Patent US9940367: Scoring candidate answer passages - query-passage scoring
- One document can be scored multiple times for different queries
- Passages scored on term match, answer term match, semantic relevance
- BERT bidirectional understanding enables passage boundary detection
- Passages scored based on author popularity, word characteristics, frequency
- Cross-attention between query tokens and passage content
- Answer passage scoring - Patent US10783156 (Baker, Venkatachary et al., filed 2018): Combines query-dependent scores (query term TF/IDF match, answer term match from top N docs) with query-independent scores (passage position, language model conformance, source reputation). Generates candidate passages from both structured (tables, lists) and unstructured content. NEWSCORE(D) = 0.8 * OLDSCORE(D) + 0.2 * SOURCERANK(SOURCE(D)). Term weight = (resources containing term) * IDF
- Weighted answer term vectors - Patent US10019513 (Google, filed 2015): Pre-computes weighted term vectors from question-answer pairs in web resources. Identifies questions via POS patterns, extracts subsequent answer text, clusters questions by similarity. At query time, scores candidate passages using term_frequency * answer_term_weight. Terms weighted by frequency across answers in the cluster, enabling lightweight real-time passage scoring against pre-computed intent-specific vectors
- US9959315: Context scoring adjustments for answer passages; US7953720: Selecting the best answer to a fact query from among a set of potential answers; US11289096: Providing answers to voice queries using user feedback
- US9146992: Enriching web resources; US5987450: System and method for obtaining complete and correct answers from incomplete and/or incorrect dat...; US9576576: Answering questions using environmental context
- US9679027: Generating related questions for search queries; US8819006: Rich content for query answers

### API Leak Signals (leak)
- SnippetBrainSelectedText - Pre-extracted passage text selected by SnippetBrain for featured snippet and passage ranking
- contentPassageScore - Per-passage relevance score stored in document index for query-time passage retrieval
- passageAnnotation - Passage-level annotation metadata including topic boundaries and semantic structure
- bodyContentBreaks - Section boundary markers used by passage segmentation to identify logical content divisions
- sectionHeadingAnchors - Heading structure signals for passage boundary detection and topical segmentation

### DOJ Trial Evidence (doj)
- Passage scoring feeds featured snippets - Testimony confirmed passage-level retrieval determines featured snippet extraction, not full-page relevance
- BERT cross-attention - HJ Kim confirmed BERT bidirectional model scores query-passage pairs, not just query-document pairs
- FastSearch passage retrieval - Elizabeth Reid affidavit: FastSearch retrieves passage-level content for AI Overview grounding, using RankEmbed signals
- DeepRank passage scoring - DeepRank applies neural re-ranking at passage level for top candidates, confirmed as one of only two ML ranking systems

### Optimization Signals (exploit)
- Clear section headers help Google identify passage boundaries
- Self-contained paragraphs that directly answer questions rank better
- Long-form content benefits when individual sections are comprehensive
- FAQ patterns with question-answer pairs align with passage scoring
- Impacts ~7% of searches (Google's stated figure at launch)
- Works alongside full-page scoring, not as replacement

### Passage Score (Patent)
Each passage within a document receives an independent relevance score based on query-passage alignment. The passage score combines term matching, semantic similarity (via BERT), and passage quality signals. A document's final ranking considers both its full-page score and its best passage score.
Formula: PassageScore(query, passage) =
  BERT_crossattn(query, passage) *
  term_match(query_terms, passage_terms) *
  quality(length, structure, uniqueness)
// ~7% of queries affected at launch
Patents: US20160078102, US9940367
Affects: Affected by: passage clarity, query-passage semantic alignment, passage completeness, BERT model

### Document Multi-Score (Patent)
A single document can now carry multiple relevance scores: one for the full page and additional scores for individual passages. The document's ranking for a given query uses the highest applicable score. This allows long-form content to rank for specific queries based on passage relevance even if the overall page topic is broader.
Formula: DocRank(query, doc) = max(
  FullPageScore(query, doc),
  max(PassageScore(query, p) for p in passages)
)
// full page still indexed, passages scored additionally
Patents: US20160078102
Affects: Affected by: page structure, passage boundaries, content depth per section

### Answer Passage Scoring (Patent US10783156 (Baker, Venkatachary et al., 2018))
Scores candidate answer passages for question queries by combining query-dependent and query-independent features. Query-dependent: TF/IDF term match score plus answer term match (terms weighted by frequency across top N documents * IDF). Query-independent: passage position in document, language model conformance, source reputation. Handles both structured (tables, lists) and unstructured (prose) content with type-specific unit selection. Source quality adjustment: NEWSCORE = 0.8 * OLDSCORE + 0.2 * SOURCERANK, where SOURCERANK incorporates 13 metrics including article production volume, breaking news score, staff size, and original named entity generation.
Formula: AnswerScore(q, passage) = QueryDependent(
  TF_IDF_match(q, passage),
  AnswerTermMatch(q, passage)
) + QueryIndependent(
  position, LM_conformance, source_rep
)

NEWSCORE(D) = 0.8 × OLDSCORE(D) +
  0.2 × SOURCERANK(SOURCE(D))
// source quality weighted at 20%

TermWeight(t) = doc_count(t) × IDF(t)
// from top N documents for query
Patent: US10783156 (Scoring candidate answer passages, 2018)
Affects: Affected by: passage clarity, query term coverage, answer term density, passage position (earlier = better), source reputation, content structure type (tables/lists scored differently from prose).

### Weighted Answer Term Scoring (Patent US10019513 (Google, 2015))
Pre-computes weighted answer term vectors from question-answer pairs found in web resources (FAQs, Q&A sites, how-to articles). The system identifies question patterns via POS tagging, extracts the subsequent answer text, clusters similar questions by semantic similarity, and generates term frequency-weighted vectors for each question cluster. At query time, candidate answer passages are scored by computing the sum of term_frequency * answer_term_weight for each term in the passage. Term weights can be scaled by the similarity between the incoming question and the question definition for the cluster.
Formula: AnswerTermScore(q, passage) =
  Σ tf(term, passage) ×
  weight(term, cluster(q))

weight(term, cluster) =
  freq(term in cluster_answers) ×
  sim(q, cluster_definition)
// pre-computed from Q&A pairs at index time
// lightweight real-time passage scoring
Patent: US10019513 (Weighted answer terms for scoring, 2015)
Affects: Affected by: presence of Q&A-style content, answer term density, alignment with common question patterns. Pages structured as clear question-answer pairs feed the pre-computation pipeline.

## Pipeline
- **Page Indexing**: Full page indexed as single document (standard process)
- **Passage Segmentation**: BERT-based NLP identifies logical passage boundaries
- **Passage Annotation**: Each passage annotated with topic, quality, and structure signals
- **Query-Passage Matching**: At query time, passages scored independently against query
- **Score Selection**: Best passage score vs. full-page score determines ranking
- **Snippet Extraction**: Highest-scoring passage used for featured snippet or result snippet

## Timeline
- 2015: Weighted Answer Term Vectors (US10019513) - Google patents pre-computed weighted answer term vectors from Q&A pairs. Clusters questions by similarity, generates intent-specific term frequency weights. Enables lightweight real-time passage scoring against pre-computed vectors.
- 2018: BERT Integration - BERT enables deep language understanding needed for passage boundary detection and scoring.
- 2020: Passage Ranking Announced - Google announces passage ranking at Search On event. 'We can now index individual passages from pages.'
- 2021: Passage Ranking Live - Launches February 2021. Impacts ~7% of queries. Martin Splitt clarifies pages still indexed whole.
- 2023: AI Overviews Connection - Passage-level retrieval feeds into AI Overview generation. FastSearch retrieves passages for Gemini grounding.
