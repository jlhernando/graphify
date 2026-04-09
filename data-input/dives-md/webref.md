# Webref: Entity Recognition & Linking

## Key Insight
Webref assigns each entity on a page a salienceScore measuring how central it is to the content (not just whether it appears). It links entities to the Knowledge Graph via kgEntityId with a kgEntityConfidence score. The topicality signal measures how relevant an entity is to the overall page theme. Critically, qrefAnnotation links queries directly to Knowledge Graph entities, enabling Google to match user intent to entity-annotated pages.

### Signals Confirmed (leak)
- salienceScores - Entity prominence measurement within a document
- kgEntityId - Knowledge Graph entity identifier linked to page content
- kgEntityConfidence - Confidence score for entity-page association
- topicality - How central an entity is to the page's overall theme
- qrefAnnotation - Query-to-Knowledge Graph entity linking for intent matching

### Signal Confirmed (doj)
- EntityAnnotations - Semantic entity recognition and linking in documents
- Part of PerDocData module (per-document metadata)
- Connects page content to structured Knowledge Graph data
- Enables entity-based retrieval beyond keyword matching
- Foundation for Knowledge Panels and rich results
- Cross-referenced with Webref module in the API leak

### 20 Patents (patent)
- Entity salience: centrality measurement beyond mere mention
- Entity disambiguation via context-aware neural models
- Knowledge Graph with billions of entity relationships
- Cross-document entity co-reference resolution
- Entity-based query understanding via qrefAnnotation
- Structured data extraction from unstructured web content
- US9875320: Providing search results based on sorted properties; US11055312: Selecting content using entity properties; US7747648: World modeling using a relationship network with communication channels to entities
- US8176055: Content entity management; US10198491: Computerized systems and methods for extracting and storing information regarding entities; US7970766: Entity type assignment
- US7769740: Systems and methods of ranking attention; US10108700: Question answering to populate knowledge base; US7333976: Methods and systems for processing contact information
- US9305108: Semantic selection and purpose facilitation; US9355140: Associating an entity with a search query; US9135238: Disambiguation of named entities
- US6735585: Method for search engine generating supplemented search not included in conventional search resul...; US8392443: Refining search queries; US7036010: Method and apparatus for a secure communications session with a remote system via an access-contr...
- US20090242620: Ratings Using Machine-Readable Representations; US6134532: System and method for optimal adaptive matching of users to most relevant entity and information ...; US10482139: Structured user graph to support querying and predictions
- US9116982: Identifying interesting commonalities between entities; US20080279369: System and method for n-dimensional encryption

### How Webref Affects Your SEO (exploit)
- Make your primary entity the salient focus, not just a mention: Webref's salienceScores measure how central an entity is to your content, not merely whether it appears. A page that mentions 'Tesla' once in passing scores low salience. A page structured entirely around Tesla, with the entity in the title, H1, opening paragraph, and throughout the content, scores high. Structure content so the primary entity is unmistakably the focal point.
- Use proper names and disambiguate entities for higher kgEntityConfidence: Webref links text mentions to Knowledge Graph nodes via kgEntityId with a confidence score. Use full, unambiguous entity names on first reference ('Apple Inc.' not just 'Apple,' 'Mercury (planet)' not just 'Mercury'). Add contextual clues that help disambiguation: industry terms, related entities, and geographic context. Higher confidence means stronger entity-query matching.
- Implement Schema.org markup to accelerate Knowledge Graph linking: Despite Google publicly saying Schema is not a ranking factor, the leak shows kgEntityId directly links page content to Knowledge Graph entities, and qrefAnnotation matches queries to entity-annotated pages. Schema markup (Organization, Person, Product, Article) gives Webref explicit entity signals rather than requiring inference from unstructured text. This is a direct pathway to entity-based retrieval.
- Build topicality by clustering content around core entities: Webref's topicality signal measures how relevant an entity is to the overall page theme. Pages that cover multiple unrelated entities dilute topicality for each one. Create focused content where all discussed entities relate to a central theme. A page about 'Python programming' should discuss related entities (Django, Flask, data science) rather than unrelated ones.
- Strengthen site2vec by maintaining domain-level entity consistency: The leak's site2vecEmbeddingEncoded aggregates all page-level embeddings into a single domain vector. Publishing content about entities outside your domain's core topic dilutes the site vector. A medical information site that publishes entertainment content weakens its entity authority. Keep your domain's entity graph coherent and tightly clustered.
- Target entity-based queries by earning a Knowledge Panel: qrefAnnotation links queries directly to Knowledge Graph entities. If your brand or organization has a Knowledge Panel, queries mentioning your entity get directly matched to your content. To earn and strengthen a Knowledge Panel: claim it via Google, maintain consistent entity information across authoritative sources (Wikipedia, Wikidata, Crunchbase), and use Schema.org Organization markup with your official site.
- Build entity co-occurrence patterns through consistent topical publishing: topicEmbeddingsVersionedData shows topic vectors evolve over time. Each time you publish quality content around your core entities, the version updates strengthen those associations. A site that publishes weekly about 'machine learning' with consistent references to related entities (neural networks, transformers, TensorFlow) builds progressively stronger entity vectors with each Webref version update.

### EntityAnnotations (DOJ)
Webref system identifies and links entities in content to Knowledge Graph entries. Maps text mentions to known entity IDs. Semantic understanding layer.
Formula: EntityAnnotations(doc) = webref(
  mentions[]
).linkToKG(kgEntityId, kgEntityConfidence)
// each mention mapped to Knowledge Graph node with confidence
Patents: US8812435 (entity extraction), US9047295 (knowledge graph)
Affects: Affected by: reference known entities clearly, use proper names, disambiguate terms. Structured data helps.

### topicEmbeddingsVersionedData (DOJ)
Versioned topic vectors evolving with content changes. Semantic classification in embedding space. Pages mapped to topic clusters. Vectors updated as content changes.
Formula: topicEmbeddings(page, v) = embed(
  entities[], topicClusters
).version(v)  // versioned, evolves with content changes
// consistent topical publishing strengthens vectors over time
Patents: US9047295 (knowledge graph)
Affects: Affected by: consistent topical publishing. Regular quality content strengthens your topic vectors over time.

### site2vecEmbeddingEncoded (DOJ)
Entire domain as single dense vector in semantic space. Aggregated from all page-level embeddings. Captures holistic site meaning. Part of QualityNsrPQData in NSR.
Formula: site2vec(domain) = aggregate(
  pageEmbedding(p) for p in domain.pages
)  // QualityNsrPQData, single dense vector per domain
// off-topic pages dilute the site vector
Affects: Affected by: site-wide topical consistency. Off-topic content dilutes your site vector.

## Pipeline
- **Entity Detection**: NLP identifies entity mentions in page content
- **KG Linking**: kgEntityId maps mentions to Knowledge Graph nodes
- **Confidence & Salience**: kgEntityConfidence + salienceScores per entity
- **Topicality**: How central each entity is to the page's theme
- **Query Matching**: qrefAnnotation links queries to KG entities for retrieval

## Timeline
- 2012: Knowledge Graph Launch - Google introduces the Knowledge Graph with 570 million entities. "Things not strings" becomes the mantra.
- 2014: Entity Salience Patents - Google patents entity salience scoring. Measuring how central an entity is to a document, not just presence.
- 2016: Webref System - Entity annotation and linking system formalized. Connects page content to Knowledge Graph entities at scale.
- 2019: Neural Entity Linking - BERT-based entity disambiguation. Context-aware linking resolves ambiguous entity mentions.
- 2024: API Leak: 5 Webref Signals - salienceScores, kgEntityId, kgEntityConfidence, topicality, qrefAnnotation confirmed in RepositoryWebref module.
- 2024: DOJ Trial: Entity Annotations - EntityAnnotations confirmed as Webref semantic entity recognition and linking system.

## Contradictions
- Public (Google Public): "Just write naturally. You don't need to optimize for entities."
  Internal (API Leak): salienceScores directly measure entity prominence. kgEntityConfidence scores how well entities are recognized. topicality measures entity centrality. Entity optimization directly impacts how Google understands your content.
- Public (Google Public): "Schema markup helps us understand your content but isn't a ranking factor."
  Internal (API Leak): Webref's kgEntityId directly links page content to Knowledge Graph entities. Pages with clear entity associations get matched to entity-based queries via qrefAnnotation. Entity recognition IS a ranking pathway.
- Public (Google Public): "We understand content holistically, not through individual signals."
  Internal (API Leak + DOJ): Webref produces granular per-entity scores: salienceScores (prominence), kgEntityConfidence (linking certainty), topicality (centrality). Each entity on a page is individually scored and linked.
