# Entity Semantic: How Google Understands Meaning

## Key Insight
site2Vec creates a site-wide semantic vector from all your content, meaning Google understands what your site is "about" holistically. Pages that diverge from the site's semantic vector may rank worse. topicEmbedding evolves over time as your content changes. RankEmbed uses dot product similarity (not cosine), meaning vector magnitude matters: more authoritative content can have "louder" embeddings.

### Signals Confirmed (leak)
- siteEmbeddings - Site-level semantic vectors aggregating all content
- pageEmbeddings - Individual page semantic vectors
- site2Vec - Word2Vec-style site-wide semantic fingerprint
- topicEmbedding - Topic relevance vector that evolves with content updates

### 2 Signals Confirmed (doj)
- topicEmbeddings - Topic-level semantic vectors (confirmed in DOJ)
- site2Vec - Site-level semantic representation (confirmed in DOJ)
- RankEmbed dual encoder: queries and docs in shared semantic space
- Dot product similarity (not cosine): magnitude carries meaning
- DocID profiles track per-document semantic evolution over time

### 6 Patents (patent)
- Word2Vec and its extensions to document and site-level representations
- Dual encoder architectures for query-document matching
- Contextual embeddings (BERT-based) for disambiguation
- Topic modeling and topical authority measurement
- Cross-lingual embeddings for multilingual understanding
- Temporal embedding evolution tracking content changes
- Entity discovery via graph reconciliation - Patent US10331706 (Google, filed 2017): Discovers new Knowledge Graph entities by extracting entity-fact tuples from web sources, clustering by name+type, then iteratively splitting via determinative predicates (one-to-one relationships like birth_date). Jaccard similarity on predicate-object pairs validates entity identity. Cross-domain corroboration (min sources and domains per bucket) ensures reliability before KG inclusion
- US6453315: Meaning-based information organization and retrieval; US8458197: System and method for determining similar topics; US6816857: Meaning-based advertising and document relevance determination
- US7158961: Methods and apparatus for estimating similarity; US6182066: Category processing of query topics and electronic document content topics

### How Entity Semantic Understanding Affects Your SEO (exploit)
- Maintain topical consistency to strengthen your site2Vec vector: Your entire domain is represented as a single dense vector in semantic space. Every page you publish shifts this vector. Off-topic content dilutes the embedding, making your site a weaker semantic match for queries in your niche. Before publishing, consider whether the content strengthens or weakens your site's semantic identity.
- Exploit dot product similarity by building authoritative, comprehensive content: RankEmbed uses dot product, not cosine similarity. This means vector magnitude matters. More authoritative, in-depth content can produce "louder" embeddings that score higher even against pages with similar topical angles but less depth. Depth and thoroughness are mathematically rewarded.
- Build topicEmbedding strength through consistent publishing cadence: Topic embeddings are versioned and evolve over time as your content changes. Consistent publication of quality content on your topic strengthens these vectors with each update. Sporadic publishing or long gaps between updates cause the embedding to weaken relative to competitors who publish steadily.
- Align new pages to your existing pageEmbedding cluster: Each page generates its own semantic vector. Pages that cluster tightly around your site's core topics reinforce the overall site embedding. Create content that naturally connects to your existing pages through shared entities, concepts, and terminology. Internal linking helps, but the semantic content itself is what shapes the embedding.
- Use entity co-occurrence to strengthen semantic vectors: Embedding models learn from entity relationships and co-occurrence patterns. Pages that mention a topic's key entities, related concepts, and contextually relevant terms produce richer vectors. Do not just target a single keyword; cover the semantic neighborhood that Google's embedding model associates with that topic.
- Monitor topical drift when expanding into adjacent areas: topicEmbedding tracks how your content evolves. Gradual expansion into closely related topics preserves embedding coherence. A sudden pivot to an unrelated topic creates semantic dissonance in your site2Vec. Expand topically by bridging from existing strengths to adjacent areas, using hub pages that connect old and new content.

### siteEmbeddings (Leak)
Site-level semantic vectors aggregating all page content into a single dense representation. Captures what a site is "about" holistically by combining individual page embeddings across the entire domain.
Formula: siteEmbedding(domain) = aggregate(
[pageEmbed(p) for p in domain.pages]
) → site_vector[d]
// captures what the site is "about" holistically
Patents: US8812435 (entity extraction), US9047295 (knowledge graph)
Affects: Affected by: all page content on the domain, content consistency, topical focus, content volume

### pageEmbeddings (Leak)
Individual page-level semantic vectors capturing page meaning in embedding space. Each page encoded as a dense vector for similarity computation against queries and other documents.
Formula: pageEmbedding(url) = encode(
text, headings, entities
) → page_vector[d]
// dense vector for similarity vs queries and docs
Patents: US9158846 (entity embedding)
Affects: Affected by: page text content, heading structure, entity mentions, semantic density

### site2Vec (Leak+DOJ)
Word2Vec-style site-wide semantic fingerprint. Part of QualityNsrPQData in NSR. Pages diverging from the site's semantic vector may rank worse. RankEmbed uses dot product similarity (not cosine), meaning vector magnitude matters.
Formula: site2Vec(domain) = word2vec_aggregate(
all_content(domain)
)  // part of QualityNsrPQData in NSR
similarity = dot_product(q, site2Vec)  // NOT cosine
// magnitude matters: authority amplifies signal
Patents: US8812435 (entity extraction), US9047295 (knowledge graph)
Affects: Affected by: site-wide content consistency, topical coherence, page-to-site vector alignment, content depth

### topicEmbedding (Leak+DOJ)
Topic relevance vector that evolves with content updates. Versioned data tracking topical shifts over time. Consistent topical coverage builds stronger embedding vectors. Part of the temporal semantic tracking system.
Formula: topicEmbedding(page, t) = evolve(
topic_vec[t-1], content_update(t)
)  // versioned, tracks topical drift
// consistent coverage builds stronger vectors
Patents: US9158846 (entity embedding), US9047295 (knowledge graph)
Affects: Affected by: content update frequency, topical consistency, semantic drift over time, topic depth

### Entity Discovery via Graph Reconciliation (Patent US10331706 (Google, 2017))
Discovers new Knowledge Graph entities by extracting entity-fact tuples from multiple web sources via relation extraction. Clusters source graphs by entity name and type, then iteratively splits clusters using determinative predicates (one-to-one relationships like birth_date, headquarters). Jaccard similarity on predicate-object pairs validates entity identity across clusters. Unreliable buckets (too few sources or domains) are discarded. Remaining graphs are merged and high-confidence candidates are flagged for Knowledge Graph inclusion.
Formula: Jaccard(E1, E2) =
  |F1 ∩ F2| /
  |F1 ∪ F2|
// F = sets of determinative predicate-object pairs

Cluster(entities) =
  group_by(name, type)
  → split_by(determinative_predicates)
  → filter(min_sources, min_domains)
  → merge(high_confidence)
// cross-domain corroboration for reliability
Patent: US10331706 (Entity discovery via graph reconciliation, 2017)
Affects: Affected by: entity mentions across multiple authoritative sources, consistency of entity facts (birth date, location, etc.), cross-domain corroboration. Entities cited consistently across diverse sources are more likely to be added to the Knowledge Graph.

## Pipeline
- **Content Ingestion**: Text extraction, entity recognition, topic identification
- **Page Embedding**: pageEmbeddings: dense vector per page from content
- **Site Aggregation**: site2Vec: site-wide vector from all page embeddings
- **Topic Tracking**: topicEmbedding: evolves over time with content updates
- **Semantic Match**: RankEmbed: dot product similarity in shared query-doc space

## Timeline
- 2013: Word2Vec - Google publishes word2vec: words as dense vectors. Foundation for all embedding-based search. Words with similar meanings cluster together.
- 2015: Site Embeddings - Google extends word2vec to entire sites. site2Vec creates site-level semantic vectors from content aggregation.
- 2017: Entity Graph Reconciliation (US10331706) - Google patents entity discovery via graph reconciliation. Extracts entity-fact tuples from web sources, clusters by name+type, splits via determinative predicates (one-to-one relationships). Jaccard similarity validates identity. Cross-domain corroboration ensures reliability for Knowledge Graph inclusion.
- 2018: BERT - Bidirectional context understanding. "Bank" means different things in "river bank" vs "bank account." Contextual embeddings.
- 2020: RankEmbed - Dual encoder model embeds queries and documents into shared semantic space. Trained on 70 days of search logs.
- 2024: API Leak: Embedding Signals - siteEmbeddings, pageEmbeddings, site2Vec, topicEmbedding confirmed as production ranking signals.
- 2024: DOJ: RankEmbed Architecture - Dual encoder with dot product (not cosine) similarity. Trained on 70 days of logs + 16K human rater IS scores.

## Contradictions
- Public (Google Public): "Keywords are still the foundation of search. We match queries to documents by terms."
  Internal (DOJ Trial): RankEmbed uses dense vector embeddings for semantic matching. Documents can rank for queries with zero keyword overlap if semantically similar in embedding space.
- Public (Google Public): "We evaluate each page independently based on its own merit."
  Internal (API Leak): site2Vec creates a site-wide semantic vector. Pages are evaluated in context of their site's overall topical profile. Off-topic pages on a site may rank worse.
- Public (Google Public): "Topical authority is not a ranking signal."
  Internal (API Leak + DOJ): topicEmbedding tracks topical relevance over time. site2Vec measures site-level topical focus. Consistent topical coverage builds stronger embedding vectors.
