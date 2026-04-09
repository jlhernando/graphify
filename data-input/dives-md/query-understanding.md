# Query Understanding: How Google Interprets Your Search

## Key Insight
Google classifies every query into 8 Refined Semantic Classes (Yes/No, Time, Numerical, Procedural, Descriptive, Comparative, List, Entity) and assigns multiple intents per document via asteroidBeltIntents. QBST is a memorization system that filters pages missing expected terminology BEFORE final ranking. Your content must contain the full constellation of salient terms that top-ranking pages share.

### Signals Confirmed (leak)
- intentScores - Document intent classification (informational, navigational, transactional)
- commercialScore - Commercial/transactional intent detection per page
- navigationalBoost - Boost for results matching navigational query intent
- referenceQueries - Previously submitted queries referring to a resource
- snippetBrain - ML-powered snippet generation and featured snippet selection
- qrefAnnotation - Entity annotation linking queries to Knowledge Graph entities
- webchooserScorer - Result type selection scorer for SERP features

### Signals,  Core (doj)
- QUS - Central query processing: classifies, rewrites, enriches queries before ranking
- Tangram - SERP assembly system: arranges blue links, features, Knowledge Panels, ads
- Tetris - Original name for Tangram. Promotes fresh content for time-sensitive queries
- asteroidBeltIntents - Multi-intent classification per document
- OfficialPageTwiddler - Boosts official/authoritative pages for brand queries
- YouTubeDensityTwiddler - Manages video result density to prevent YouTube overrepresentation

### 38 Patents (patent)
- Query rewriting: synonym identification via co-occurrence (US8538984), concept-based term substitution (US9104750), semantic unit extraction from queries (US7249121), combining parameters across related queries (US11762848), query canonicalization (US20200167379)
- Query parsing: ambiguous query resolution (US7136854), NL query parsing without retraining (US11914627), user-context-based search (US9449105), classifying web blocks by linguistics (US7895148)
- Answer extraction: multi-source short answer scoring (US20230342411), sentence compression for snippets (US9336186), search result filters from content (US11797626)
- Authority signals: Agent Rank author reputation (US7565358), trust-based ranking (US7603350), authoritative results (US9659064), editorial opinion in ranking (US7096214)
- Document analysis: query-specific duplicate detection (US6615209), document clusters by related words (US7383258), information extraction (US6678681), entity-based query resolution (US7536382)
- Modern AI search: stateful chat search with generative companion (US20240289407), multi-modal ML (US10789427), AI-generated content pages (US12536233), privacy-preserving personalized LLMs (US20240403564)
- US6381594: System and method for personalized information filtering and alert generation; US20090164425: System and method for crawl ordering by search impact; US6778979: System for automatically generating queries
- US7610282: Rank-adjusted content items; US8055669: Search queries improved based on query semantic information; US9852225: Associating features with entities, such as categories of web page documents, and/or weighting su...
- US9552388: System and method for providing search query refinements; US6711568: Method for estimating coverage of web search engines; US20090327283: Techniques for web site integration
- US20100211566: Entity-based search results and clusters on maps; US7991780: Performing multiple related searches; US7958109: Intent driven search result rich abstracts
- US7672932: Speculative search result based on a not-yet-submitted search query; US7725485: Generating query suggestions using contextual information

### How Query Understanding Affects Your SEO (exploit)
- Map content to Google's 8 Refined Semantic Classes: Google classifies queries as Yes/No, Time, Numerical, Procedural, Descriptive, Comparative, List, or Entity. Structure your content to explicitly match these formats. A 'how to' query expects Procedural content (numbered steps), while 'best X vs Y' expects Comparative (side-by-side analysis). Mismatching the semantic class means QUS routes your page away from the query.
- Build multi-intent pages to maximize asteroidBeltIntents coverage: The DOJ confirmed documents are tagged with a constellation of potential query matches, not a single intent. A comprehensive guide that covers definitions, comparisons, how-tos, and pricing for a topic will be assigned more intents, making it eligible for a wider range of queries from one URL.
- Align page commercialScore with query intent: The leak's commercialScore classifies pages by transactional intent. If you are targeting informational queries, minimize aggressive product mentions, pricing CTAs, and affiliate links that inflate your commercial score. Conversely, product pages should include clear pricing, availability, and purchase options to match transactional intent.
- Secure the OfficialPageTwiddler boost for your brand: DOJ testimony confirmed OfficialPageTwiddler boosts official pages for navigational queries. Strengthen your brand's association with your domain: consistent branding across platforms, Google Business Profile claiming, Schema.org Organization markup, and building branded search volume. This is how Google identifies your domain as the 'official' result.
- Optimize for QUS query expansion, not just exact keywords: QUS performs synonym expansion, entity resolution, and query reformulation before retrieval. Research what synonyms, related terms, and entity associations Google connects to your target queries. Tools like Google's own autocomplete, People Also Ask, and related searches reveal QUS expansion patterns you should cover.
- Structure content for Tangram SERP feature eligibility: Tangram (formerly Tetris) assembles the SERP layout based on query classification. To win featured snippets, use concise definition paragraphs (40-60 words). For PAA boxes, structure content as clear Q&A pairs. For video carousels, embed relevant video with proper Schema. Each SERP feature has specific content format requirements that Tangram evaluates.
- Use referenceQueries to understand how Google associates pages with queries: The leak confirmed referenceQueries tracks previously submitted queries referring to a resource. Build content that naturally attracts diverse query associations by covering subtopics comprehensively. The more unique queries that lead to clicks on your page, the more referenceQueries strengthen your retrieval for related terms.

### QUS (DOJ)
Central query processing service that classifies, rewrites, and enriches queries BEFORE retrieval. Performs spell correction, synonym expansion, entity resolution, and intent classification. All downstream ranking depends on QUS output.
Formula: QUS(raw_query, ctx) = {
corrected: spell_fix(q),
expanded: synonyms(q),
entities: resolve(q, KG),
intent: classify(q)
}  // runs BEFORE retrieval
Patents: US8903810 (query classification), US9152665 (query interpretation), US8538984 (synonym identification), US9449105 (user-context search), US11914627 (NL query parsing)
Affects: Affected by: raw query text, user context (language, location, device), search history, entity database

### asteroidBeltIntents (DOJ+Leak)
Multiple intents assigned per document. Documents tagged with a constellation of potential query matches rather than a single intent. Enables one page to rank for diverse query intents based on its content coverage.
Formula: asteroidBeltIntents(doc) = [
intent_1(w1), intent_2(w2), ..., intent_N(wN)
]  // constellation of potential query matches
// one page can rank for diverse intents
Patents: US8903810 (query classification)
Affects: Affected by: document content breadth, entity mentions, topic coverage, semantic analysis depth

### Tangram (DOJ)
SERP assembly system (formerly called Tetris). Arranges blue links, featured snippets, Knowledge Panels, ads, and video results. Decides what result types to show and in what layout based on query classification and available content.
Formula: Tangram(query_class, results) = assemble(
blue_links, snippets, KP, ads, video
)  // formerly called Tetris
// layout driven by query classification
Patents: US9152665 (query interpretation), US11797626 (search result filters from content)
Affects: Affected by: query type classification, available result types, ad inventory, content format availability

### commercialScore (Leak)
Per-page commercial/transactional intent score. Classifies how transactional a page's content is. Exact scoring mechanism not confirmed.
Formula: commercialScore(page) = classify_intent(
product_mentions, pricing, transactional_lang
) → commercial_probability
// per-page transactional intent classification
Patents: US8903810 (query classification)
Affects: Affected by: page content indicators, product mentions, pricing data, transactional language patterns

### Synonym Expansion (Hummingbird) (Patent)
Identifies query term synonyms by analyzing co-occurrence with non-adjacent terms across search logs. When a concept is detected in a query, substitution candidates are drawn from other queries containing that same concept. This is the patent basis for Hummingbird's semantic matching.
Formula: synonymScore(t1, t2) = cooccurrence(t1, t2, non_adjacent) × conceptMatch(t1, t2)
// co-occurrence across query logs, not just page content
substitute(q, t1) = argmax(synonymScore(t1, candidates))
// concept-aware term substitution
Patents: US8538984 (synonym identification via co-occurrence), US9104750 (concept-based term substitution)
Affects: Affected by: query log co-occurrence data, concept detection, term frequency, substitution confidence thresholds

### Semantic Unit Extraction (Patent)
Identifies meaningful multi-word phrases (semantic units) within queries by checking how frequently candidate compound terms appear in top-ranked documents. Weighted by document relevance rank. Prevents splitting meaningful phrases into individual keywords.
Formula: semanticUnit(terms) = compound(t_i, t_j) where
freq(compound, top_docs) × rank_weight > θ
// compound terms validated against top-ranked documents
Patents: US7249121 (semantic units from search queries)
Affects: Affected by: top-ranked document content, compound term frequency, document relevance weights

### Stateful Chat Search (Patent)
Augments search with a generative companion that maintains conversation state across query turns. Classifies queries using contextual information from prior turns, generates synthetic queries to fill gaps, and combines parameters from related queries sharing a line of inquiry.
Formula: statefulSearch(q_t, history) = {
context: mergeState(q_1...q_{t-1}),
synthetic: generateQuery(q_t, context),
combined: mergeParams(q_t, related_queries)
}  // generative companion with cross-turn memory
Patents: US20240289407 (search with stateful chat), US11762848 (combining multi-query parameters)
Affects: Affected by: conversation history, query semantic similarity, generative model output, user session state

### Short Answer Extraction (Patent)
Generates featured snippet callouts by scoring candidate passages from multiple sources. Trains a scoring engine on passage quality signals including content, titles, and surrounding context. Sentence compression removes non-essential terms to produce concise answers.
Formula: answerScore(passage) = scoreEngine(
content, title, context, non_candidates
)
snippet = compress(top_passage, edge_weights)
// multi-source scoring + sentence compression
Patents: US20230342411 (multi-source short answer scoring), US9336186 (sentence compression)
Affects: Affected by: passage content quality, source diversity, title relevance, compression feature weights

### Agent Rank (Patent)
Assigns reputation scores to content agents (authors/creators) based on digital signatures associating them with content. Agent reputation depends on quality and link profiles of their signed content. Multiple agents can contribute to a single page, each scored independently. Feeds into trust-based ranking.
Formula: agentRank(agent) = Σ quality(signed_content) × linkProfile(content)
trustRank(page) = f(agentRank(contributors), seed_trust)
// author reputation feeds page trust scoring
Patents: US7565358 (Agent Rank), US7603350 (trust-based ranking)
Affects: Affected by: agent digital signatures, content quality signals, link profiles, seed trust set distance

## Pipeline
- **Query Input**: Raw user query + context (language, location, device, history)
- **QUS Processing**: Spell correction, synonym expansion, entity resolution, intent classification
- **QBST Filtering**: Salient term matching: pages missing expected terms filtered out
- **Retrieval**: Enriched query sent to index with intent weights and entity annotations
- **Tangram Assembly**: SERP layout: blue links, featured snippets, PAA, KP, AI Overviews, video

## Timeline
- 2003: Query Rewriting - Early patents on spell correction, synonym expansion, and query reformulation for better retrieval.
- 2004: Semantic Units - Patent US7249121 on extracting meaningful multi-word phrases from queries by validating compound terms against top-ranked documents.
- 2005: Agent Rank - Patent US7565358 introduces author/agent reputation scores based on digital signatures and content quality. Precursor to E-E-A-T author signals.
- 2009: Entity-Based Queries - Google files patents on resolving queries to Knowledge Graph entities (US7536382). Queries become entity lookups.
- 2013: Hummingbird / Synonym ID - Patent US8538984 on synonym identification via co-occurrence analysis. Enables semantic matching beyond exact keywords. Foundation of the Hummingbird update.
- 2015: RankBrain - ML system handles 15% of queries Google has never seen. Learns query-document relationships from click patterns.
- 2019: BERT Integration - BERT processes 1 in 10 queries for contextual understanding. Prepositions and word order now affect meaning.
- 2023: Short Answer Extraction - Patent US20230342411 on multi-source passage scoring for featured snippet callouts. Trains scoring engine on candidate and non-candidate passages.
- 2024: Stateful Chat + AI Pages - Patents US20240289407 (search with generative companion maintaining conversation state) and US12536233 (AI-generated content pages tailored per user). Query understanding enters the LLM era.
- 2024: API Leak: QUS Pipeline - referenceQueries, intentScores, commercialScore, navigationalBoost, snippetBrain, qrefAnnotation all confirmed.
- 2024: DOJ: Tangram + QBST - QUS processes queries BEFORE retrieval. Tangram/Tetris assembles SERP layout. QBST memorizes salient terms. 8 semantic query classes.

## Contradictions
- Public (Google Public): "We don't rewrite queries. We show results for what you typed."
  Internal (DOJ Trial): QUS extensively rewrites queries: spell correction, synonym expansion, entity resolution, and intent reclassification all happen before retrieval. The query you typed is rarely the query that runs.
- Public (Google Public): "Search results are based on relevance, not commercial intent."
  Internal (API Leak): commercialScore explicitly classifies pages by transactional intent. navigationalBoost applies direct ranking boost for branded queries. Intent classification drives result selection.
- Public (Google Public): "We treat all content types equally in search results."
  Internal (DOJ Trial): YouTubeDensityTwiddler explicitly manages video result density. OfficialPageTwiddler boosts brand pages. Tangram optimizes SERP layout per query type. Content types are ranked and slotted differently.
