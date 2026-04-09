# Knowledge Graph Construction: How Google Builds Its Fact Repository

## Key Insight
Google's Knowledge Graph is not manually curated. It is built by a bootstrapping loop: seed facts generate dependency-parse patterns, patterns extract new facts from the corpus, and each fact is corroborated by counting how many documents independently support it. Entity synonyms are discovered via anchor text frequency (how many pages link to the entity's source using a given name). The system handles the long tail: rare entities with few mentions are extracted using patterns learned from common entities.

### 15 Patents (patent)
- Seed fact bootstrapping (US9672251) - Start with known (subject, attribute, object) triples. Find sentences expressing each fact, compute dependency parse graphs, extract minimal sub-graphs spanning subject/attribute/object tokens, replace tokens with variables to create reusable patterns.
- Pattern application at corpus scale - Apply dependency-parse patterns across the entire document corpus. Any sentence matching a pattern's graph structure yields a new candidate fact. Patterns are scored by frequency (how many extractions) and coherence (average pairwise distance between extracted attribute vectors).
- Template-based extraction (US8812435) - Complementary approach for structured sources: extract title patterns (how entity names appear in document titles) and contextual patterns (how attribute-value pairs appear in infobox/table structures). Apply both patterns to discover new entities and facts from template-heavy pages like Wikipedia.
- Long-tail entity coverage - Dependency-parse patterns learned from common entities (millions of mentions) transfer to rare entities (tens of mentions). A pattern learned from 'Barack Obama was born in Honolulu' applies to any sentence matching the same syntactic structure.
- US8825471: Unsupervised extraction of facts; US7454398: Support for object search; US20090063550: Fact-based indexing for natural language search
- US8812509: Inferring attributes from search queries; US10467256: Automatic query pattern generation; US20090327223: Query-driven web portals
- US10452694: Information extraction from question and answer websites; US8347202: Determining geographic locations for place names in a fact repository; US10346485: Semi structured question answering system
- US8751498: Finding and disambiguating references to entities on web pages; US20090282010: Creation and enrichment of search based taxonomy for finding information from semistructured data; US20090076799: Coreference Resolution In An Ambiguity-Sensitive Natural Language Processing System
- US7590628: Determining document subject by using title and anchor text of related documents

### 2 Patents (patent)
- Hypothesis testing across the corpus (US8954412) - Strip noise words from a query, search the fact repository AND document corpus for matching terms, generate hypothetical facts (candidate attribute values), count how many documents independently support each hypothesis.
- Containment filter - A fact is only presented if it is not a substring of another fact with equal or greater document support. Prevents partial answers when the full answer has equal backing.
- Central entity identification (US9009192) - Build a global entity co-occurrence graph weighted by pointwise mutual information (PMI). For a target document, filter the graph to retain only entities found in that document. Entities with no connections to other document entities are removed (they are peripheral, not topically central).
- Central entity scoring - Combines: sum of outgoing edge weights in the filtered graph, query log frequency, frequency in target resource, and corpus-wide frequency. Central entities define what the page is 'about'.

### 4 Patents (patent)
- Anchor text synonym discovery (US8738643) - Find the source document(s) for an entity. Collect all anchor texts from pages linking to it. Normalize, filter (blacklist 'click here', etc.), score by frequency and proportion. 'IBM', 'Big Blue', 'International Business Machines' are discovered as synonyms for the same entity.
- Knowledge panel contextualization (US11720577) - Standard panels show static facts. This system detects context terms in the query (e.g., 'Taylor Swift albums') and reshapes the panel to show relationship knowledge elements (each album with metadata) ranked above generic entity facts.
- Quote search via Knowledge Graph (US9727617) - Match query to KG entities, traverse relationship edges to find associated entities, retrieve quotes from a quote database, score by relevance + recency + frequency.
- Answer passage scoring (US10019513) - Offline: cluster question phrases from the corpus, extract answer sections, build weighted answer-term vectors per cluster. Online: match incoming question to a cluster, score candidate passages against the pre-computed answer-term vector.

### How Knowledge Construction Affects Your SEO (exploit)
- Structure your facts for extraction: Google's template-based extraction (US8812435) works best on structured content: tables, definition lists, clear attribute-value pairs. If your page contains facts about an entity, present them in patterns that machines can parse (schema markup, consistent formatting, clear subject-predicate-object structure).
- Be the corroboration source: Facts are validated by counting independent document support (US8954412). If your site is one of the documents that corroborates a fact, you become part of the fact's provenance chain. Original research, data, and primary sources contribute to fact corroboration.
- Anchor text shapes entity identity: Entity synonyms are discovered via inbound anchor text (US8738643). If you want your brand recognized as an entity, ensure inbound links use your brand name as anchor text, not generic 'click here'. Multiple name variants in anchors teach Google your entity's alternative names.
- Central entity clarity matters: Pages with too many unrelated entities will have their central entity identification diluted (US9009192). The PMI-based graph filtering removes entities that don't connect to others in the document. Focus each page on a coherent entity cluster.
- Question-answer patterns are pre-computed: Google clusters question phrases and pre-builds answer-term vectors (US10019513). If your content naturally answers common questions with the terms Google expects, it scores higher for featured snippets. Study 'People Also Ask' patterns to understand what answer-term vectors look like for your topic.

### Dependency Pattern Coherence Score (Patent US9672251 (Whang, Halevy et al.))
Each extraction pattern is scored by combining extraction frequency with semantic coherence. Coherence measures whether the attributes extracted by a pattern are semantically related (using high-dimensional vector distances). Incoherent patterns that extract random attributes are penalized.
Formula: pattern_score(p) = frequency(p) × coherence(p)
// frequency = total extractions by pattern p
coherence(p) = avg(
  pairwise_distance(attr_vectors)
)  // lower distance = more coherent = better
if pattern_score > threshold: apply pattern corpus-wide
Patent: US9672251 (Fact extraction via dependency patterns)
Affects: Affects: which facts enter the Knowledge Graph. High-coherence patterns produce reliable facts; low-coherence patterns are discarded.

### Central Entity Score (PMI Graph) (Patent US9009192 (Bar-Yossef, Matias et al.))
Identifies which entities on a page are topically central versus peripheral. Builds a global entity co-occurrence graph weighted by PMI, then filters to the page's entities. Entities with no connections to other page entities are removed. Survivors are scored by edge weight sum + query frequency + document frequency.
Formula: PMI(A,B) = log(
  P(A,B) / (P(A) × P(B))
)  // high PMI = entities strongly co-occur
central_score(entity, doc) = 
  sum(edge_weights_in_filtered_graph) +
  α × query_frequency +
  β × doc_frequency +
  γ × corpus_frequency
Patent: US9009192 (Central entity identification)
Affects: Affects: what Google thinks your page is 'about'. Peripheral entities are filtered out. Central entities determine topicality and Knowledge Graph association.

### Fact Corroboration Count (Patent US8954412 (Zhao, Czuba))
Counts independent document support for each hypothetical fact. A candidate fact (subject + attribute value) is corroborated if N or more documents contain both the subject entity and the candidate value in meaningful proximity. Containment filter ensures partial facts don't shadow complete answers.
Formula: support(fact) = count(
  docs where subject AND value
  appear in contextual proximity
)
if support > threshold: fact = corroborated
if fact ⊂ longer_fact AND
  support(longer_fact) ≥ support(fact):
  suppress shorter fact
Patent: US8954412 (Corroborating facts in electronic documents)
Affects: Affects: which facts are presented as answers. Original data sources that independently confirm facts contribute to corroboration count.

### Entity Synonym Score (Patent US8738643 (Czuba))
Discovers alternative names for entities by analyzing anchor text of inbound links to the entity's source documents. Each anchor text variant is scored by frequency and proportion relative to all anchors. Variants above threshold become synonyms; below-threshold candidates are blacklisted.
Formula: synonym_score(name, entity) = 
  α × frequency(name_in_anchors) +
  β × proportion(name / total_anchors)
if score > min_threshold: add as synonym
else: add to blacklist
Patent: US8738643 (Learning synonymous object names from anchor texts)
Affects: Affects: entity resolution. Whether Google recognizes 'IBM', 'Big Blue', and 'International Business Machines' as the same entity depends on anchor text patterns across the web.

## Pipeline
- **Seed Facts**: Start with known (subject, attribute, object) triples from hand-crafted rules or existing KG
- **Pattern Extraction**: Generate dependency-parse patterns from seed facts + template patterns from structured sources
- **Corpus-Scale Application**: Apply patterns across document corpus to extract candidate facts
- **Corroboration**: Count independent document support for each candidate fact. Filter by containment
- **Entity Resolution**: Discover synonyms via anchor text. Merge entity variants. Build central entity graph
- **Knowledge Panel Generation**: Contextualize panels by query terms. Rank relationship knowledge elements above static facts

## Timeline
- 2005: Brin files pattern bootstrapping (US6678681) - Sergey Brin's DIPRE (Dual Iterative Pattern Relation Extraction) patent: extract entity tuples from web text by bootstrapping patterns from seed examples. Foundation for all later extraction work.
- 2008: Zhao files fact learning from documents (US8812435) - Template-based extraction using title and contextual patterns from structured sources like Wikipedia infoboxes.
- 2009: Zhao/Czuba file fact corroboration (US8954412) - Hypothesis testing across the corpus: generate candidate facts, count independent document support, filter by containment.
- 2011: Bar-Yossef/Matias file central entity identification (US9009192) - PMI-weighted co-occurrence graph for determining which entities on a page are topically central versus peripheral.
- 2012: Knowledge Graph launched publicly - Google announces the Knowledge Graph with 500 million entities and 3.5 billion facts. Built using the extraction, corroboration, and synonym systems patented 2005-2011.
- 2013: Czuba files entity synonym discovery (US8738643) - Anchor text analysis across inbound links to discover alternative entity names. Enables query-entity matching regardless of name variant.
- 2013: Whang/Halevy file dependency-pattern extraction (US9672251) - Bootstrapping loop: seed facts generate dependency-parse patterns, patterns extract new facts, scored by frequency and coherence.
- 2016: Knowledge panel contextualization (US11720577) - Context-sensitive knowledge panels that reshape content based on query terms. 'Taylor Swift albums' shows album-focused panel, not generic Taylor Swift bio.
- 2024: API leak confirms KG integration depth - The Google Content Warehouse API leak reveals entityId, topicEmbedding, kgEntityId, and knowledgeResultType fields, confirming deep Knowledge Graph integration with ranking signals.

## Contradictions
- Public (Google (various)): Google uses structured data and schema markup to understand entities on your page.
  Internal (Patents US9672251, US8812435, US9009192): Schema markup is one signal, but the primary entity understanding comes from dependency-parse extraction, template matching, co-occurrence graphs, and anchor text analysis. The system was designed to work on unstructured text at corpus scale, not just pages with markup.
- Public (Google Search Central): Knowledge Panels are automatically generated from various sources across the web.
  Internal (Patent US11720577): Knowledge Panels are not just aggregated facts. The system actively reshapes panel content based on query context, relationship traversal, and personalization signals. The 'same' entity shows different panels depending on query terms.
