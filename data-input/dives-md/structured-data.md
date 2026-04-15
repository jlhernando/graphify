# Structured Data & Schema.org: From Markup to Knowledge Graph

## Key Insight
Structured data does not teach Google what your page is about. Webref identifies entities via NLP regardless of markup. What Schema.org actually does is confirm entity associations (boosting kgEntityConfidence), disambiguate entities (strengthening kgEntityId linkage), and declare relationships (like isAuthor for authorship and encodedAccountableEntity for E-E-A-T). The 7 confirmed API signals show Google already knows your entities. Markup makes that knowledge more precise and unlocks rich result eligibility.

### API Leak Signals (leak)
- kgEntityId - Knowledge Graph entity ID linked to page. Schema.org markup (Organization, Person, Product) helps Webref resolve the correct KG entity when multiple candidates exist.
- kgEntityConfidence - Confidence score for entity-page association. Structured data acts as a confirming signal, raising confidence when markup aligns with NLP-extracted entities.
- topicality - How central an entity is to the page's content. Schema.org's mainEntity and about properties explicitly declare topicality.
- encodedAccountableEntity - Structured entity representation used for E-E-A-T evaluation. Maps to Schema.org's Organization and Person types with credential properties.
- salienceScores - Entity prominence within a document. Structured data reinforces salience by declaring which entities are primary subjects versus incidental mentions.
- isAuthor - Boolean flag for author entity attribution. Triggered by Schema.org Person markup within Article.author property. Connects content to author's Knowledge Graph entity.
- qrefAnnotation - Query reference entity annotation mapping queries to KG entities. Structured data on authoritative pages helps define which entities queries should resolve to.

### 4 Patents (patent)
- Combining content with search results (US9947026) - System for integrating structured content annotations directly into search result presentation. Enables rich results by extracting structured fields (ratings, prices, dates, recipes) from markup and combining them with standard search snippets. The patent describes how structured annotations are validated against page content before display.
- Storing semi-structured data (US9754048) - Architecture for ingesting and storing semi-structured data from web pages at scale. Handles the heterogeneity of Schema.org implementations across millions of sites. Normalizes varying markup quality into a consistent internal representation for downstream consumption by Knowledge Graph and rich result systems.
- Generating structured information (US7788293) - Method for automatically generating structured data from unstructured web content. When pages lack explicit markup, the system infers structure using template detection, DOM analysis, and pattern matching. This is Google's fallback when Schema.org is absent, extracting the same information less reliably.
- Question answering to populate knowledge base (US10108700) - Uses question-answer pairs extracted from the web to populate Knowledge Graph attributes. Structured data from FAQ and QAPage markup feeds this system directly, turning declared Q&A content into Knowledge Graph facts.

### Structured Data for Rich Results (exploit)
- Use Schema.org to disambiguate, not describe: Google already extracts entities via Webref NLP. Structured data's real value is disambiguation. If your page is about Mercury (the planet), Schema.org type declaration resolves the ambiguity that NLP alone cannot. Focus markup on identity (sameAs, identifier, @type) rather than restating what the body text already says.
- Declare your accountable entity for E-E-A-T: encodedAccountableEntity maps to Organization and Person markup. Include full organizational details (name, url, sameAs to Wikipedia/Wikidata, foundingDate) and author credentials. This is the structured signal that feeds Google's E-E-A-T assessment pipeline.
- Connect isAuthor through Person markup: The isAuthor boolean is triggered by Schema.org Article.author linking to a Person entity with a Knowledge Graph presence. Author pages with sameAs links to LinkedIn, Google Scholar, or Wikipedia create the strongest isAuthor signal.
- Maximize rich result eligibility via US9947026: Rich results require valid structured data that matches page content. Google validates markup against visible content before displaying rich snippets. Mismatches between markup claims and page content trigger rich result suppression, not ranking penalties.
- Feed the Knowledge Graph with FAQ and QAPage markup: US10108700 shows Q&A content feeds KG population. FAQ markup on authoritative pages can populate Knowledge Graph attributes, creating a feedback loop where your structured answers become Google's knowledge.
- Implement mainEntity to boost topicality: The topicality signal measures entity centrality. Schema.org's mainEntityOfPage and about properties explicitly declare which entity the page is primarily about, reinforcing what Webref detects algorithmically.

### System Context (doj)
- Knowledge Graph as SERP infrastructure - DOJ testimony established the Knowledge Graph as central to Google's search result presentation. Knowledge Panels, featured snippets, and entity carousels all depend on confident entity identification, which structured data supports.
- Entity understanding drives ranking diversity - Trial evidence showed Google uses entity recognition to diversify results. When a query maps to multiple entity interpretations, structured data on pages helps Google classify which interpretation each result serves.
- Webref as the primary entity system - Internal documents confirm Webref performs entity recognition independently of markup. Structured data is an input to Webref, not a replacement for it. Pages without markup still receive full entity analysis.

### Calculations
- StructuredDataConfidenceBoost: When Schema.org markup aligns with Webref's NLP-extracted entities, kgEntityConfidence receives a boost. The system compares declared entity types and properties against independently extracted entity annotations. Agreement raises confidence; contradiction has no effect (markup is ignored, not penalized). Affects: Affected by: alignment between Schema.org types and actual page content. Markup that matches what Webref already detects raises confidence. Markup that contradicts page content is discarded.
- RichResultEligibility: Determines whether a page qualifies for enhanced SERP presentation (rich snippets, carousels, FAQ expansions). Validates that structured data fields match visible page content. Checks markup completeness against required fields for each rich result type. Pages passing validation enter the rich result candidate pool. Affects: Affected by: complete and accurate structured data matching visible content. Missing required fields or content mismatches prevent rich result display.
- AccountableEntityResolution: Resolves the accountable entity behind content for E-E-A-T evaluation. Combines Schema.org Organization/Person markup with Knowledge Graph lookups. The encodedAccountableEntity signal stores the resolved entity, while isAuthor flags individual author attribution. Both feed into quality scoring systems. Affects: Affected by: Organization/Person markup with sameAs links to authoritative sources (Wikipedia, Wikidata, LinkedIn). Author pages with KG presence create the strongest signal.

### Structured Data Processing Pipeline
- Markup Extraction: Parse JSON-LD, Microdata, RDFa from page HTML. Normalize to internal schema representation.
- Fallback Inference: US7788293: For pages without markup, infer structure via DOM analysis, template detection, pattern matching.
- Entity Resolution: Match declared entities against Knowledge Graph. Resolve kgEntityId via sameAs links and name matching.
- Webref Cross-validation: Compare markup entities with NLP-extracted entities. Aligned signals boost kgEntityConfidence.
- Rich Result Validation: US9947026: Validate markup completeness and content alignment. Determine rich result eligibility per type.
- KG Population: US10108700: Ingest validated facts into Knowledge Graph. FAQ/QA markup feeds attribute population.
- SERP Assembly: Combine validated structured fields with search snippets. Render rich results, Knowledge Panels, carousels.

### Timeline
- 2006 (patent): Generating Structured Information (US7788293) - Google patents methods for automatically extracting structured data from unstructured web content. Establishes the foundation for processing web pages without explicit markup.
- 2011 (patent): Schema.org Launch - Google, Bing, Yahoo, and Yandex co-launch Schema.org vocabulary. First standardized structured data format for search engines. Initially supports limited types.
- 2012 (patent): Knowledge Graph + Structured Data - Knowledge Graph launches with 570M entities. Schema.org markup becomes a direct input for entity confirmation and Knowledge Panel population.
- 2014 (patent): Semi-Structured Storage (US9754048) - Google patents architecture for ingesting heterogeneous structured data at web scale. Handles inconsistent Schema.org implementations across millions of sites.
- 2017 (patent): Rich Results Expansion (US9947026) - Patent for combining structured content annotations with search results. Rich snippets expand to recipes, events, products, FAQ, and HowTo types.
- 2024 (leak): API Leak: 7 Entity Signals Confirmed - kgEntityId, kgEntityConfidence, topicality, encodedAccountableEntity, salienceScores, isAuthor, qrefAnnotation confirmed in ranking API. Structured data's role as confirming signal becomes visible.
- 2025 (patent): Rich Result Type Deprecations - Google deprecates several structured data types including ClaimReview, Dataset, and Speakable. Signals shift from publisher-declared markup toward algorithmic extraction.

### Contradictions
- Public (Google Public): "Structured data helps Google understand your content better." vs Internal (API Leak + Internal Systems): Webref identifies entities via NLP independently of any markup. Structured data is a confirming signal that boosts kgEntityConfidence, not the primary method of content understanding. Pages without markup receive full entity analysis.
- Public (Google Public): "Structured data is not a ranking factor." vs Internal (API Leak): kgEntityConfidence, salienceScores, and topicality directly influence ranking. Structured data boosts these signals when it aligns with NLP extraction. While markup itself is not a direct ranking factor, the entity confidence signals it strengthens are.
- Public (Google Public): "You should add structured data so Google can better understand your page." vs Internal (Patent US7788293): Google patents and deploys systems to automatically generate structured information from unstructured content. The system extracts the same entity data whether you add markup or not. Markup makes extraction more reliable but is not required.

### Overview
Google processes Schema.org markup as one input into its entity understanding pipeline, but internally, structured data is a confirming signal rather than the primary source of entity identification. Webref and NLP extract entities independently. Markup helps disambiguation, triggers rich results, and feeds the Knowledge Graph population pipeline. The API leak reveals 7 entity signals that structured data directly influences.
