# QueryClassification: Google's Query Intent Detection System

## Key Insight
QueryClassification operates before ranking begins, making it one of the most consequential systems in the pipeline. Its output determines which branch of the ranking architecture is activated: navigational queries weight brand signals and queriesForWhichOfficial heavily; informational queries activate BERT/MUM semantic matching and snippet generation; transactional queries trigger commercial intent signals and shopping integrations. This means the same page can rank very differently depending on how Google classifies the query, even if the page content is identical. Understanding query classification helps explain why rankings shift between query variations.

### DOJ Trial: Sworn Testimony (doj)
- Query intent classification: Google categorizes queries into navigational, informational, transactional, and local intents. Each category activates different ranking signal configurations and SERP feature sets. Confirmed in testimony about the ranking pipeline.
- SERP feature gating: Query classification directly determines which SERP features are eligible. Featured snippets only appear for informational queries. Shopping results only for transactional. Site links primarily for navigational. This gating happens before Tangram assembly.

### UPX1044: Personalization Is Rare (Gomes/Sullivan, Nov 2018) (doj)
- Danny Sullivan: 'Personalization is rare and light' - Query itself is the dominant signal, not user profile
- No demographic profiling: Google confirmed no demographic data is used in search ranking or query classification
- Top Stories never personalized: News results are not tailored to individual user preferences

### Related Leak Evidence (leak)
- No direct QueryClassification signals in the API leak, but related systems confirm classification outputs are used throughout the pipeline:
- queriesForWhichOfficial (PerDocData): Pages recognized as the official result for navigational queries. Directly depends on QueryClassification identifying a query as navigational.
- isNavigational: Query-level flag used by NavBoost and Twiddlers. Navigational queries receive different engagement thresholds than informational ones.
- queryTopicality: Topic matching between query and document, calibrated differently by query type.

### 21 Patents (patent)
- No patents directly mapped to QueryClassification as a named system. Query classification is likely covered under broader query understanding patents.
- Webref (entity resolution): Entity detection in queries feeds into classification. Queries mentioning known entities are more likely navigational.
- BERT/MUM: Neural language models interpret query semantics, which aids intent classification for ambiguous queries.
- Query classification is a well-studied area in IR research. Google's implementation likely combines ML classifiers with heuristic rules (query length, entity presence, modifier words like 'buy' or 'how to').
- Machine translation for query expansion - Patent US20080319962 (Riezler, Vasserman, filed 2008): Uses statistical machine translation to expand queries with context-aware synonyms. Three training approaches: question-answer pairs, query-snippet pairs (from click logs), and phrase-paraphrase pairs (via back-translation). p(paraphrase|input) = max_trans[p(translated|input) * p(paraphrase|translated)]. Context maps prevent incorrect expansions (e.g., 'ship' expands to 'send' not 'boat' in 'how to ship a box')
- Semantic interpretation evaluation - Patent US10353964 (Google, filed 2015): Evaluates ambiguous query intent by generating canonical query templates for each semantic interpretation. Substitutes entity references with disambiguated versions, then compares result set similarity (Jaccard intersection, keyword frequency, ranking order) between original and modified queries. Higher similarity indicates the interpretation likely matches user intent
- US20090299964: Presenting search queries related to navigational search queries; US8412699: Fresh related search suggestions; US20100262615: Generating Improved Document Classification Data Using Historical Search Results
- US8046350: Systems and methods for detecting commercial queries; US20100179929: SYSTEM FOR FINDING QUERIES AIMING AT TAIL URLs; US20080065617: Search entry system with query log autocomplete
- US8140562: Method and system for displaying real time trends; US20110060736: Query Revision Using Known Highly-Ranked Queries; US6502091: Apparatus and method for discovering context groups and document categories by mining usage logs
- US9740780: Autocompletion using previously submitted query data; US20100205198: Search query disambiguation; US8346757: Determining query terms of little significance
- US7487145: Method and system for autocompletion using ranked results; US6826564: Scalable and programmable query distribution and collection in a network of queryable devices; US20100257164: Search queries with shifting intent
- US20090119289: Method and System for Autocompletion Using Ranked Results; US8838587: Propagating query classifications; US7409383: Locating meaningful stopwords or stop-phrases in keyword-based retrieval systems
- US9213748: Generating related questions for search queries; US9213748B1: Generating related questions for search queries

### 8 Refined Query Semantic Classes (Candour Exploit) (exploit)
- Google endpoint exploit revealed 8 'Refined Query Semantic Classes' used to categorize queries
- 1. Short fact - queries seeking brief factual answers
- 2. Boolean - yes/no or true/false questions
- 3. Instruction - how-to and procedural queries
- 4. Definition - what-is queries seeking definitions
- 5. Reason - why queries seeking explanations
- 6. Comparison - queries comparing two or more items
- 7. Consequence - queries about outcomes or effects
- 8. Other - queries not fitting above categories
- Significant portion of queries classified as 'short fact' or 'Boolean'
- Classification impacts SERP layout, featured snippet eligibility, and ranking approach
- YMYL queries (health, financial) receive different ranking criteria and stricter evaluation

### How Query Classification Affects Your Traffic (exploit)
- Map your keywords by intent: Before optimizing, understand how Google classifies each target query. Check SERPs for feature patterns: site links = navigational, featured snippets = informational, shopping = transactional. Optimize for the intent Google assigns.
- Navigational queries are winner-take-all: If Google classifies a query as navigational, the brand/official site dominates. Competing for someone else's brand name is nearly impossible. Focus on owning your own brand queries.
- Informational queries unlock rich features: Featured snippets, PAA, and Knowledge Panels only appear for informational classifications. Structure content with clear answers, headers, and lists to capture these features.
- Transactional queries have commercial signals: For queries classified as transactional, product schema, pricing, availability, and reviews become ranking factors. E-commerce sites should ensure structured data is complete.
- Mixed-intent queries are opportunities: Some queries have ambiguous classification (e.g., 'best laptop' could be informational or transactional). Google may show both types of results, giving you a chance to appear with the right content format.
- Local intent is geographic: Queries classified as local trigger the map pack and local results. For local businesses, Google Business Profile optimization and local citations matter more than traditional SEO.
- Query modifiers shift classification: Adding 'how to,' 'what is,' or 'best' changes classification. Target long-tail variations that match your content's intent alignment.

### Query Intent Classification (DOJ)
Queries are classified into primary intent categories before entering the ranking pipeline. The classification determines which ranking configuration is activated, which signals are weighted most heavily, and which SERP features are eligible. Classification likely uses a combination of ML models (BERT-based), entity detection (Webref), query pattern matching, and historical click data.
Formula: queryClass(query) = classify(
  semantics(query),   // BERT/MUM analysis
  entities(query),     // Webref entity detection
  patterns(query),     // modifier words, length
  clickHistory(query)  // NavBoost navigation patterns
)
// Output: navigational | informational | transactional | local
// Some queries get mixed classification
Affects: Affected by: query wording, entity presence, historical user behavior for this query, geographic context

### SERP Feature Gating (DOJ)
Based on query classification, specific SERP features are enabled or disabled before Tangram assembles the layout. This gating reduces computation (no need to evaluate snippet quality for navigational queries) and improves relevance (transactional queries get shopping results, not Wikipedia articles).
Formula: eligibleFeatures(queryClass) =
  switch(queryClass) {
    navigational: [siteLinks, knowledgePanel]
    informational: [featuredSnippet, PAA, KP, images]
    transactional: [shopping, ads, reviews, prices]
    local: [mapPack, localBusiness, reviews]
  }
// Tangram only assembles eligible features
Affects: Affected by: query classification output. Features not eligible for the query class will never appear regardless of content quality.

### Semantic Interpretation Evaluation (Patent US10353964 (Google, 2015))
Evaluates multiple semantic interpretations for ambiguous queries by generating canonical query templates for each interpretation. For each candidate intent, the system creates a modified query with disambiguated entity references and runs it against the index. Result set similarity between original and modified queries (measured via Jaccard intersection, keyword frequency overlap, or ranking order comparison) indicates which interpretation best matches user intent. Higher similarity means the interpretation aligns with what the original query already retrieves, confirming it as the likely intent.
Formula: IntentScore(interpretation) =
  similarity(
    results(original_query),
    results(modify(query, interpretation))
  )

Jaccard = |R_orig ∩ R_mod| /
  |R_orig ∪ R_mod|
// or keyword frequency, or rank order comparison
// highest similarity = most likely user intent
Patent: US10353964 (Evaluating semantic interpretations, 2015)
Affects: Affected by: query ambiguity, entity presence, result set diversity. Ambiguous queries benefit most from multi-interpretation evaluation.

### Context-Aware Query Expansion (Patent US20080319962 (Riezler, Vasserman, 2008))
Expands queries with context-aware synonyms using statistical machine translation (SMT). Trained on three parallel corpora: question-answer pairs (FAQ documents), query-snippet pairs (click-through logs filtered by dwell time), and phrase-paraphrase pairs (via back-translation through pivot language). Context maps store left/right word context to prevent incorrect expansions. The system 'translates' a query into a paraphrased version, extracting synonyms by comparing original and translated word-by-word.
Formula: p(paraphrase | input) =
  maxtrans[
    p(translated | input) ×
    p(paraphrase | translated)
  ]
// trained via EM on parallel corpora

Expand(query) = query OR
  synonym(term, context)
// context prevents: 'ship' -> 'boat'
// context allows: 'ship' -> 'send' (in 'how to ship a box')
Patent: US20080319962 (Machine translation for query expansion, 2008)
Affects: Affected by: query context (surrounding words), training corpus quality (Q&A pairs, click logs), synonym disambiguation. Long-tail queries benefit most from expansion.

## Pipeline
- **Query Received**: Raw query string received by SuperRoot. Spelling correction, query expansion, and tokenization applied.
- **Entity Detection**: Webref identifies entities in the query. Known entities (brands, people, places) influence classification toward navigational or local intent.
- **Intent Classification**: QueryClassification assigns primary intent: navigational, informational, transactional, or local. Mixed intents possible for ambiguous queries.
- **Pipeline Configuration**: Based on classification, RankingArchitecture activates the appropriate signal configuration. Different signal weights for each intent category.
- **SERP Feature Gating**: Eligible SERP features determined by classification. Only features matching the query intent are candidates for Tangram assembly.
- **Ranking Execution**: Mustang retrieval, Ascorer scoring, and Twiddler adjustments execute with the intent-specific configuration selected by QueryClassification.

## Timeline
- ~2008: Universal Search and Intent - Google launches Universal Search, blending web, image, video, and news results. Query classification becomes essential for deciding which verticals to include.
- 2008: Query Expansion via Machine Translation (US20080319962) - Google patents context-aware query expansion using statistical machine translation. Trained on Q&A pairs, query-snippet click logs, and back-translated paraphrases. Context maps prevent incorrect synonym expansion. p(paraphrase|input) computed via EM on parallel corpora.
- 2015: Semantic Interpretation Evaluation (US10353964) - Google patents multi-interpretation query disambiguation. Generates canonical query templates per interpretation, compares result set similarity (Jaccard, keyword frequency, ranking order) between original and modified queries. Higher similarity indicates correct user intent interpretation.
- ~2012: Knowledge Graph Integration - Knowledge Graph launch makes entity-based query classification more important. Queries about known entities trigger Knowledge Panels.
- 2019: BERT for Query Understanding - BERT enables better semantic understanding of query intent, improving classification accuracy for ambiguous and long-tail queries.
- 2024: DOJ Trial: Classification Confirmed - Query intent classification confirmed as a pre-ranking system that gates SERP features and determines which ranking signal configurations are activated.
- 2018: Personalization Confirmed as Rare (UPX1044) - Danny Sullivan and Gomes (Nov 2018): 'Personalization is rare and light.' Query is the dominant signal. No demographic profiling used in search. Top Stories never personalized. Confirms query classification, not user profiling, drives ranking configuration.
- 2024: Candour: 8 Query Semantic Classes Revealed - Mark Williams-Cook (Candour Agency) endpoint exploit exposed Google's 8 Refined Query Semantic Classes: Short fact, Boolean, Instruction, Definition, Reason, Comparison, Consequence, Other. Most queries fell into 'short fact' or 'Boolean' categories.
