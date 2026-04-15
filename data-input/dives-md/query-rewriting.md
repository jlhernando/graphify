# Query Rewriting: How Google Transforms Your Search Before Ranking

## Key Insight
The query you type is not the query Google ranks for. QUS transforms raw queries through multiple processing stages before retrieval. Spell correction fixes typos. Synonym expansion adds equivalent terms (US8538984: co-occurrence-based). Entity resolution links terms to Knowledge Graph entities. Semantic unit extraction identifies multi-word phrases (US7249121: 'New York' stays together). Concept substitution via RankBrain maps unknown terms to known equivalents (US9104750). The DOJ trial confirmed QUS as a central system running BEFORE retrieval, and QBST (Query Based Salient Terms) memorizes which words 'should appear prominently on web pages relevant to that query,' enabling Google to filter documents by expected terminology before final ranking.

### Signals, Core (doj)
- QUS (QueryUnderstandingService) - Central query processing system that interprets, classifies, and enriches search queries before they reach the ranking pipeline. Performs spell correction, synonym expansion, entity resolution, intent classification. Runs BEFORE retrieval (DOJ trial testimony)
- QBST (Query Based Salient Terms) - Memorization system identifying words appearing prominently on relevant pages. Trained on 13 months of query and click data. Used in initial retrieval to identify candidate document set. Pages missing expected terminology filtered before final ranking (Lehman testimony)
- 18 Search Quality Aspects - 'Spell correction control' listed as one of 18 quality dimensions evaluated by human raters. Confirms spell correction as a core quality metric, not just convenience
- RefinedQuerySemanticClass - 8 semantic classes for query classification: short fact, boolean, instruction, definition, reason, comparison, consequence, other. Determines how query rewriting is applied (Candour exploit)
- DebunkingQueryClassifier - Detects queries like 'is earth flat?' and forces consensus-only results. Query classification affects not just ranking but which results are eligible

### 10 Key Patents (patent)
- Synonym identification via co-occurrence (US8538984): Identifies synonyms using co-occurrence with non-adjacent terms. Analyzes confidence values for term substitution. Foundation for Hummingbird semantic matching
- Concept-based query substitution (US9104750, Strohmann, RankBrain co-creator): Replaces unknown terms with conceptually equivalent known terms using concept contexts. Core RankBrain technology
- Semantic unit extraction (US7249121, filed 2000): Identifies meaningful multi-word phrases in queries. Validates compound terms against top-ranked documents. Prevents splitting 'New York' into 'new' + 'york'
- Machine translation for query expansion (US20080319962): Context-aware expansion using statistical machine translation. Trained on Q&A pairs, query-snippet click logs, phrase-paraphrase pairs. Context maps prevent 'ship' expanding to 'send' vs 'boat'
- Word2Vec embeddings (US9740680, Mikolov, Chen, Corrado, Dean): Words as vectors preserving semantic relationships. vec('King') - vec('Man') + vec('Woman') = vec('Queen'). Foundation for RankBrain query interpretation
- Query rewriting with entity detection (US7536382): Detects entity names, determines associations, generates rewritten queries with entity disambiguation
- Evaluating semantic interpretations (US10353964, 2015): Evaluates multiple semantic interpretations for ambiguous queries. Compares result set similarity via Jaccard intersection
- Spell-checking search queries (US7194684): Dedicated spell correction system for search queries
- Augmenting queries with synonyms (US7475063): Synonym selection using language statistics for query augmentation
- Generating query variants via generative model (US11663201): Modern approach using trained generative models to produce query reformulations

### API Leak Signals (leak)
- queryTermEmbedding - Per-query-term embedding vectors stored for concept substitution and nearest-neighbor lookup
- nluAnnotation - Natural language understanding annotations enabling concept extraction from queries
- semanticRelevanceScore - ML-derived relevance score combining concept matching with document features
- novelQueryClassification - Classification flag for queries requiring RankBrain rewriting vs standard keyword matching
- RankEmbedNearestNeighbors - Vector similarity retrieval using RankBrain-era embeddings for concept matching
- qrefAnnotation - Entity annotation linking queries to Knowledge Graph entities
- intentScores - Document intent classification (informational, navigational, transactional) affecting query processing

### How Query Rewriting Affects SEO (exploit)
- You rank for the rewritten query, not the typed query: Google rewrites queries before ranking. A typo, a synonym, or a conceptual equivalent all route to the same rewritten query. Optimize for the canonical concept, not just exact keywords
- Semantic units protect multi-word terms: 'Machine learning engineer' is identified as a semantic unit, not three separate words. Content that uses established multi-word terms naturally aligns with Google's semantic unit extraction
- QBST filters by expected terminology: Pages missing the words QBST expects for a query are filtered before final ranking. If QBST memorizes that 'Python tutorial' pages should contain 'def', 'print', 'import', pages missing these terms may be excluded from candidates
- Synonym expansion broadens your reach: Google expands queries with synonyms (US8538984). Content using varied vocabulary for the same concept naturally captures these expansions. Synonym richness improves discoverability
- Entity resolution connects queries to Knowledge Graph: If your content is linked to KG entities via WebRef, it benefits from entity-based query rewriting. Structured data and clear entity references help
- Context-aware expansion prevents mismatches: US20080319962 uses context maps to prevent incorrect expansions. Google distinguishes 'apple fruit' from 'Apple company' before expanding. Clear topical context in content helps Google apply the right expansions

### Calculations
- QUS Processing Pipeline: QUS processes raw queries through multiple stages before retrieval: spell correction, synonym expansion, entity resolution via Knowledge Graph, intent classification, and query annotation. The output is an enriched query object that Mustang uses for retrieval. Affects: Affected by: query vocabulary, spelling accuracy, entity disambiguation, language complexity. QUS determines what documents are even considered.
- Synonym Expansion Score: Identifies synonyms by analyzing co-occurrence patterns with non-adjacent terms. If 'car' and 'automobile' frequently co-occur with the same non-adjacent terms ('insurance', 'repair', 'used'), they are identified as synonyms. Confidence values determine whether substitution occurs. Affects: Affected by: term frequency, co-occurrence patterns, concept overlap. Synonym expansion determines which additional terms your content can rank for.
- Context-Aware Query Expansion (Machine Translation): Uses statistical machine translation (SMT) trained on three parallel corpora: Q&A pairs, query-snippet click logs, and phrase-paraphrase pairs. Translates queries into 'meaning space' and back to find context-appropriate expansions. Context maps prevent incorrect expansions (e.g., 'ship' in a maritime context expands to 'vessel', not 'send'). Affects: Affected by: training corpus quality, context signal strength, translation model accuracy.
- QBST Salient Term Filtering: QBST memorizes which terms should appear prominently on pages relevant to specific queries. Trained on 13 months of user data. During retrieval, pages missing expected salient terms are filtered from the candidate set before Ascorer scoring. This is a pre-ranking filter, not a ranking signal. Affects: Affected by: query-document term overlap. Pages missing expected terminology are excluded before scoring. Content must contain the terms users associate with the topic.

### Query Rewriting Pipeline
- Raw Query Input: User types query into search box. Raw text includes potential typos, abbreviations, ambiguities, and novel terms.
- Spell Correction: Spelling errors corrected using language models and query log patterns (US7194684). 'Did you mean?' shown when correction confidence is moderate.
- Semantic Unit Extraction: Multi-word phrases identified and preserved (US7249121). 'New York Times' kept as a unit, not split into three words.
- Synonym Expansion: Synonyms identified via co-occurrence analysis (US8538984). Query expanded with equivalent terms. Context-aware expansion via SMT (US20080319962).
- Entity Resolution: Query terms linked to Knowledge Graph entities via WebRef. Disambiguates 'apple' (fruit vs company) based on query context.
- Intent Classification: Query classified into semantic classes (navigational, informational, transactional, local) and refined classes (short fact, boolean, instruction, etc.).
- RankBrain Concept Substitution: For novel queries (~15% of searches), Word2Vec maps unknown terms to nearest known concepts. 'Temperature coefficient of the opposite of night' becomes 'temperature coefficient of day.'
- QBST Term Filtering: Candidate documents checked for expected salient terms. Pages missing key terminology filtered before ranking begins.

### Timeline
- 2000 (patent): Semantic Unit Extraction (US7249121) - First patent on identifying meaningful multi-word phrases in search queries. Foundation for phrase-level query understanding.
- 2006 (patent): Spell Correction Patent (US7194684) - Dedicated method for spell-checking search queries using language models and query log statistics.
- 2008 (patent): Machine Translation for Query Expansion - US20080319962: Uses statistical machine translation across three parallel corpora for context-aware query expansion.
- 2012 (patent): Synonym Identification (US8538984) - Co-occurrence-based synonym detection. Non-adjacent term patterns identify semantic equivalences. Foundation for Hummingbird.
- 2013 (event): Hummingbird Update - Major query processing overhaul enabling semantic understanding. Query rewriting shifts from keyword matching to concept matching.
- 2015 (event): RankBrain Deployed - Word2Vec-based concept substitution handles ~15% of never-seen queries. Third most important ranking factor at launch.
- 2019 (event): BERT Integration - BERT added for query understanding. Affects 1 in 10 queries initially. Complements RankBrain (BERT understands context; RankBrain maps concepts).
- 2024 (leak): QUS Signals Exposed - API leak reveals queryTermEmbedding, nluAnnotation, semanticRelevanceScore, novelQueryClassification, and RankEmbedNearestNeighbors signals.
- 2025 (doj): QUS Confirmed in Trial - DOJ testimony confirms QUS as central query processing system. QBST confirmed as salient term memorization system trained on 13 months of data.

### Contradictions
- Public (Google Search Central (how search works)): Google matches your search terms to relevant web pages. We look at the words in your query and find pages that match. vs Internal (DOJ: QUS pipeline + 119 query rewriting patents): Google extensively rewrites queries before matching. Spell correction, synonym expansion, concept substitution, entity resolution, and semantic unit extraction transform the raw query into a fundamentally different search. The query Google ranks for may share few words with what you typed. 119+ patents cover this transformation pipeline.
- Public (Google (public guidance on keyword optimization)): Use the words people would search for in your content. Think about what terms users might use. vs Internal (DOJ: QBST + Patent US8538984 (synonym expansion)): QBST memorizes which specific terms should appear on relevant pages, creating a terminology filter that excludes pages missing expected words regardless of their quality. Synonym expansion means Google matches your content against expanded query terms you cannot see. The gap between public keyword guidance and the actual query processing pipeline makes pure keyword optimization unreliable.
- Public (Google (public communications on search quality)): We're getting better at understanding the intent behind your searches. AI helps us understand what you really mean. vs Internal (DOJ: Nayak testimony + HJ Kim PXR0356): Only 2 ML systems operate in core ranking (RankBrain + DeepRank). Query rewriting uses a mixture of hand-crafted rules (spell correction, synonym dictionaries) and older ML techniques (Word2Vec from 2013, co-occurrence statistics). The 'AI understanding' narrative overstates the role of modern AI; most query processing uses statistical methods from the 2000s and 2010s, refined over decades of query log analysis.

### Overview
Before any ranking happens, Google's Query Understanding Service (QUS) rewrites your search query through spell correction, synonym expansion, entity resolution, semantic unit extraction, and concept substitution. With 119+ patents on query processing and DOJ confirmation of QUS as a central system, query rewriting is one of Google's deepest competitive moats. RankBrain handles the ~15% of never-before-seen queries by mapping unknown concepts to known patterns via Word2Vec. The 2008 Machine Translation for Query Expansion patent (US20080319962) revealed Google uses statistical machine translation between languages to find paraphrases, with context maps preventing incorrect expansions.
