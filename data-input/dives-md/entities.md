# Entity Understanding & Topicality

## Key Insight
The T* (Topicality) score is built from exactly three sub-signals: A* (Anchors), B* (Body), C* (Clicks). A* measures "what the web says about a document" through link context. B* measures "what the document says about itself." C* measures "what users say" through click behavior. This ABC framework is the core of query-document relevance, supplemented by entity-level understanding from the Webref system.

### Webref & Entity Signals (leak)
- salienceScores - Entity prominence within a document
- kgEntityId - Knowledge Graph entity ID linked to page
- kgEntityConfidence - Confidence of entity-page association
- topicality - How central an entity is to the page's content
- qrefAnnotation - Query-to-entity mapping via Knowledge Graph
- author - Document author(s) as structured data
- isAuthor - Boolean for entity authorship attribution

### T* Score & YMYL (doj)
- T* - Topicality score with A*/B*/C* sub-signals
- A* (Anchors) - "What the web says about a document"
- B* (Body) - "What the document says about itself"
- C* (Clicks) - "What users say about a document"
- titlematchScore - Query-to-title relevance alignment
- avgTermWeight - Content emphasis via font sizing
- ymylHealthScore - Health content authority assessment
- chard - YMYL domain authority signal

### 7 Patents (patent)
- Knowledge Graph - 570M+ entities connected to search
- Webref - Bridges documents to KG entities
- Entity salience - Not just mentioned, but central to the page
- E-A-T - Expertise, Authoritativeness, Trustworthiness
- QBST - Memorizes words that appear on relevant pages
- Knowledge-Based Trust (KBT) - Research (2015) proposing ranking by factual accuracy. Extracts knowledge triples (subject, predicate, object) and compares against Knowledge Graph/Vault. Tested on 2.8B triples across 119M pages. Never deployed to production Search (confirmed via multiple sources including Mark Traphagen, 2015). The capability for entity-level fact verification exists at scale.
- Knowledge Vault - 16 extraction systems building a massive fact database. Automatic triple extraction and cross-referencing. Feeds entity validation pipeline.
- Patent US20120158633: Knowledge Graph based search system. Patent US9547823: KG-based content recommendation.
- Entity-based effectiveness ranking - Patent US9767159 (filed 2014): Ranks resources for entity queries (artist, album, movie) using an effectiveness measure combining: (1) quantity of steps to access content (fewer = higher score), (2) user affinity for publisher (app installed, account exists, usage frequency, social connections), (3) aggregate user behavior. Connects web search with mobile app ecosystems via deep links. Personalizes rankings based on installed apps and user history
- Bill Slawski's extensive analysis of entity extraction patents
- US20030195834: Automated online purchasing system; US10977573: Distantly supervised wrapper induction for semi-structured documents; US5010478: Entity-attribute value database system with inverse attribute for selectively relating two differ...
- US7257604: System and method for communicating information relating to a network resource

### How Entity Understanding Affects Your SEO (exploit)
- Build your T* score across all three pillars: Topicality is computed from A* (what the web says via anchors), B* (what the page says via body), and C* (what users say via clicks). Most SEOs focus only on B* (on-page). Build A* through strategic link building with topically relevant anchor text. Improve C* by earning satisfied clicks with compelling SERP snippets.
- Maximize entity salience, not just mentions: Webref's salienceScores measure how central an entity is to the page, not merely whether it appears. A page that briefly mentions a topic scores low; a page structured around the entity as its core subject scores high. Make your target entity the clear focus of the page through title, headings, opening paragraph, and Schema.org markup.
- Connect your content to Knowledge Graph entities: kgEntityId and kgEntityConfidence measure how strongly your page connects to known Knowledge Graph entities. Reference real entities (people, places, organizations, concepts) by their proper names. Use Schema.org markup (Organization, Person, Product) to help Webref confidently link your content to KG entries.
- Strengthen A* by earning contextually described links: The DOJ confirmed A* measures "what the web says about a document" through anchor text and link context. Links with descriptive, topically relevant surrounding text contribute more to your T* score than naked URLs or generic "click here" anchors. Guest posts and citations in contextually relevant articles build the strongest A* signals.
- Prepare for YMYL scrutiny if your content touches health or finance: ymylHealthScore and chard are explicit algorithmic signals, not just quality rater guidelines. YMYL content faces stricter authority thresholds. Demonstrate credentials, cite authoritative sources, include author bios with relevant expertise, and ensure factual accuracy. The algorithmic bar is higher regardless of what Google publicly states.
- Use Schema.org structured data to assist entity disambiguation: qrefAnnotation maps queries to Knowledge Graph entities. Structured data (Organization, Person, MedicalCondition, FinancialProduct) helps Google's entity recognition system resolve ambiguity. A page about "Mercury" ranks differently depending on whether Google associates it with the planet, element, or car brand. Structured data removes guesswork.

### EntityAnnotations (DOJ: Webref semantic entity recognition and linking)
Webref system identifies entities in content and links them to Knowledge Graph entries. Semantic entity recognition maps text mentions to known entities.
Formula: EntityAnnotations(page) = Webref(
  text_mentions → kgEntityId, confidence, salience
)  // links text to Knowledge Graph entries
// salience: is entity central or just mentioned?
Patents: US8812435 (entity extraction from documents), US9047295 (knowledge graph construction)
Affects: Affected by: referencing known entities clearly. Use proper names, disambiguate terms. Structured data (Schema.org) helps entity recognition.

### topicEmbeddingsVersionedData (DOJ: versioned topic embedding vectors)
Versioned topic vectors that evolve over time as content changes. Semantic classification using embedding space. Pages are mapped to topic clusters.
Formula: topicEmbeddings(page, v) = embed(
  content[version_v]
) → topic_cluster  // versioned, evolves over time
// consistent topical content strengthens vectors
Patents: US9047295 (knowledge graph), US8812435 (entity extraction)
Affects: Affected by: consistent topical content. Topic embeddings evolve, so regular quality content on your topic strengthens vectors.

### site2vecEmbeddingEncoded (DOJ: site-level vector embedding in semantic space)
Entire domain represented as a single dense vector in semantic space. Aggregated from all page-level embeddings. Captures "what the site is about" holistically.
Formula: site2vec(domain) = aggregate(
  page_embeddings[all_pages]
) → dense_vector  // single site representation
// off-topic pages dilute the embedding
Patents: US9047295 (knowledge graph construction)
Affects: Affected by: site-wide topical consistency. All pages contribute to the site vector. Off-topic content dilutes the embedding.

### asteroidBeltIntents (DOJ: intent classification for query type and specificity)
Classifies search intent type and specificity. Maps queries to intent categories. Determines which signals and ranking approaches apply to a query.
Formula: asteroidBeltIntents(query) = classify(
  intent_type, specificity
) → signal_routing  // determines ranking approach
// maps queries to intent categories for signal selection
Affects: Affected by: not directly controllable. But understanding intent categories helps create content that matches how Google classifies related queries.

### EntityEffectiveness (Patent US9767159 (Google, filed 2014))
For entity-specific queries (artist, album, movie, product), ranks resources by an effectiveness measure combining access friction and user affinity. Access friction: how many steps required after clicking to view/play/buy the content (fewer steps = higher rank). User affinity: whether user has publisher's app installed, account/subscription, social connections, usage frequency, explicit preferences. Aggregate behavior: what other users chose for this entity. Connects search with app deep links for personalized entity results.
Formula: Effectiveness(resource, user, entity) = f(
  steps_to_content,  // fewer = higher score
  user_affinity: {
    app_installed, has_account,
    usage_frequency, social_connections
  },
  aggregate_behavior  // what others chose
)
// personalizes entity query results via deep links
// connects web search with app ecosystems
Patent: US9767159 (Ranking search results, 2014)
Affects: Affected by: content accessibility (direct deep links vs multi-step navigation), user's installed apps and accounts, publisher relationship signals, aggregate user preferences for entity content sources.

### Knowledge-Based Trust (KBT) (Research: Google (2015), VLDB)
Research system proposing ranking by factual accuracy instead of link popularity. Extracts knowledge triples (subject, predicate, object) from web pages using information extraction. Compares triples against Knowledge Graph and Knowledge Vault. Pages with more facts matching known truth receive higher trust scores. Pages with factual errors receive lower scores. Tested on 2.8 billion triples across 119 million pages. John Mueller confirmed KBT is research only, not in production Search. However, the capability to verify factual claims against the Knowledge Graph exists and may inform other signals like contentEffort.
Formula: KBT(page) = score(
  extract_triples(page)
  vs
  KnowledgeGraph + KnowledgeVault
)
// Triple: (subject, predicate, object)
// More correct facts = higher trust
// 2.8B triples tested across 119M pages
// STATUS: Research only, never deployed to production Search
Research: KBT Paper (VLDB 2015)
Affects: Affected by: factual accuracy of claims, alignment with Knowledge Graph entities, entity attribute correctness. AI-generated content that hallucinates facts would score low.

## Pipeline
- **Entity Extraction**: Webref: salience, kgEntityId, confidence
- **KG Linking**: Map entities to Knowledge Graph, qrefAnnotation
- **T* Scoring**: A* (anchors) + B* (body) + C* (clicks)
- **YMYL Check**: ymylHealthScore, chard, ymylNewsScore
- **Ascorer**: T* combined with Q* and P* for final ranking

## Timeline
- 2010: Freebase & Entity Extraction - Google acquires Freebase. Entity recognition patents filed for connecting documents to real-world entities.
- 2012: Knowledge Graph Launch - Google introduces Knowledge Graph with 570M entities. Entity understanding becomes central to search.
- 2015: RankBrain: Query Understanding - ML system for interpreting novel and ambiguous queries. Connects query intent to entity relationships.
- 2014: Entity Effectiveness Ranking (US9767159) - Google patents entity-based ranking using effectiveness measure: combines access steps (fewer = better), user affinity (app installed, account, usage, social), and aggregate behavior. Personalizes entity query results via deep links connecting web search with app ecosystems.
- 2018: E-A-T & YMYL Classification - Quality rater guidelines formalize entity authority. YMYL categories get stricter quality requirements.
- 2015: Knowledge-Based Trust Research - Google researchers publish KBT: ranking by factual accuracy via knowledge triple extraction. Tested on 2.8B triples across 119M pages. Not deployed to production.
- 2024: API Leak: Webref System - salienceScores, kgEntityId, kgEntityConfidence, topicality, qrefAnnotation confirmed in ranking.
- 2024: DOJ Trial: T* with A/B/C - Topicality score uses three sub-signals: Anchors, Body, Clicks. YMYL signals ymylHealthScore, chard confirmed.
- 2025-06: ClaimReview Deprecation - Google deprecates ClaimReview structured data along with 6 other types. Fact-checking signals shift from publisher-declared markup to algorithmic assessment via models like MUM and contentEffort. Rankings unaffected.

## Contradictions
- Public (Google Public): "There is no YMYL algorithm. YMYL is a concept from our quality rater guidelines, not a ranking signal."
  Internal (DOJ + Leak): ymylHealthScore, ymylNewsScore, and chard are explicit algorithmic signals in the ranking pipeline. YMYL is not just guidelines but actual code.
- Public (Google Public): "We match queries to relevant content based on keywords and meaning."
  Internal (DOJ Trial): T* uses exactly three sub-signals (A, B, C). Anchor text (A*) remains critical for relevance. "What the web says about you" still outweighs on-page optimization alone.
- Public (Google Public): "Authorship markup (rel=author) is deprecated and not used in ranking."
  Internal (API Leak): author and isAuthor fields exist in the ranking pipeline. Entity-level authorship attribution connects content to Knowledge Graph entities.
