# Authorship: Google's Author Identity System

## Key Insight
The most revealing finding is the authorObfuscatedGaiaStr signal: an obfuscated string linking page authors to Google's GAIA identity system, the same authentication backend used for Gmail, YouTube, and Google accounts. This means Google can potentially cross-reference an author's identity across its entire ecosystem. Combined with isAuthor (a Tier 1 core signal with 100/100 corroboration and 19 matched patents), Google has a robust system for identifying WHO wrote content, not just evaluating the content itself. Google publicly denied having an 'author authority' metric (2016-2019), while these signals existed internally, a pattern similar to their denial of 'domain authority' while siteAuthority existed in Q*.

### 13 Patents (patent)
- Patent US9864795: Author identification and ranking - Methods for identifying content authors and using authorship as a ranking signal
- Patent US8589399: Content author quality scoring - Evaluating author expertise based on publication history and topic consistency
- Patent US10534810: Author entity resolution - Disambiguating authors across different sites and publications
- Patent US7536382: Expert identification in web content - Identifying subject matter experts based on content analysis and citation patterns
- Patent US11275895: Generating author vectors - Neural network generates floating-point vector representations of authors from writing style. Can verify authorship by comparing document vectors against stored author vectors. Distance below threshold confirms authorship.
- Patent US8150842: Reputation of an author of online content - Computes author reputation from peer reviews, authentication scores, and per-topic reputation. Authors accumulate reputation across publications with topic-specific scores
- 19+ patents total, spanning author identification, quality scoring, entity resolution, expert detection, peer reputation, and stylometric verification. The volume indicates sustained investment in authorship as a ranking dimension
- US8458196: System and method for determining topic authority; US7302638: Efficiently displaying and researching information about the interrelationships between documents; US7246310: Efficiently displaying and researching information about the interrelationships between documents
- US20090271391: Method and apparatus for rating user generated content in seach results; US7752208: Method and system for detection of authors; US7184949: Basic poetry generation
- US20110004693: Reputation Mashup

### API Leak: Author Signals (leak)
- isAuthor (PerDocData, Entity system): Boolean flag identifying distinct authorship on a page. Tier 1, 100/100 corroboration, core signal
- authorObfuscatedGaiaStr (PerDocData): Obfuscated author identity linked to Google's GAIA authentication system
- isAuthor categorized under content-quality signal subcategory, confirming it's a quality evaluation metric
- The Entity system attribution means authorship detection is built on Google's entity recognition infrastructure (Webref)
- GAIA integration means Google can cross-reference author identity across Gmail, YouTube, Google Scholar, and other services

### DOJ Trial: Authorship System (doj)
- isAuthor confirmed under a separate Authorship system (distinct from Entity), suggesting a dedicated quality evaluation layer
- authorObfuscatedGaiaStr confirmed as author credential storage linked to GAIA identity
- The dual system attribution (Entity in leak, Authorship in DOJ) suggests the pipeline: Entity detects authors -> Authorship evaluates author quality
- Related to E-E-A-T (Experience, Expertise, Authoritativeness, Trustworthiness) from Google's Quality Rater Guidelines
- Google publicly stated authorship markup (rel=author) was discontinued in 2014, but internal authorship signals continued

### How Authorship Affects Rankings (exploit)
- Author consistency matters: isAuthor is a boolean, meaning Google first checks if distinct authorship exists. Attributed content > anonymous content
- GAIA connection is powerful: Authors with Google accounts (especially Google Scholar, YouTube, Gmail) may have richer identity profiles
- Cross-site author signals: US10534810 patents entity resolution across sites. An author's reputation follows them across publications
- Topic expertise clusters: US8589399 evaluates author quality by topic consistency. Authors who consistently write about specific topics score higher
- E-E-A-T is real but indirect: No explicit 'E-E-A-T score' exists, but authorship signals feed into Q* quality scoring which gates features like snippets
- Author pages help: Having clear author bio pages with structured data helps Google's entity resolution connect your content to your identity
- YMYL amplification: In Your Money or Your Life topics, author credentials carry even more weight. Medical, financial, and legal content benefits most from strong authorship signals
- Author Vectors detect AI content shifts: Patent US11275895 enables Google to build a writing-style fingerprint per author. If an established author suddenly publishes content with a radically different style vector (e.g., AI-generated), the distance between document vector and stored author vector increases, flagging a potential authorship mismatch. This creates a passive AI detection mechanism without needing explicit AI classifiers.

### Author Identity Resolution (Leak+Patent)
Google's authorship pipeline first detects whether a page has a distinct author (isAuthor boolean). If yes, it attempts to resolve the author's identity using entity recognition (Webref) and optionally links to the GAIA identity system via authorObfuscatedGaiaStr. This creates an author entity that can accumulate reputation signals across publications.
Formula: AuthorIdentity(doc) =
  detectAuthor(doc) ?
    resolveEntity(
      author_signals(doc),
      GAIA_lookup
    ) : null
// isAuthor: boolean detection of distinct authorship
// authorObfuscatedGaiaStr: GAIA identity link
// Webref entity resolution across publications
Patents: US9864795, US10534810
Affects: Affected by: author bio presence, structured data (Person schema), Google account association, publication history

### Author Quality Scoring (Patent)
Once an author entity is resolved, Google can evaluate author quality based on publication history, topic consistency, citation patterns, and cross-site reputation. This feeds into the broader Q* quality pipeline. Authors with consistent expertise in specific topics, high-quality publication venues, and positive user engagement on their content score higher.
Formula: AuthorQuality(author) =
  f(
    topic_consistency,
    publication_quality,
    citation_patterns,
    engagement_signals
  )
// Topic consistency: staying in your lane
// Publication quality: where you publish
// Feeds into Q* quality scoring pipeline
Patents: US8589399, US7536382
Affects: Affected by: author's topic expertise breadth/depth, publication venue quality, author's content engagement metrics

### Author Vector Stylometry (Patent US11275895)
A neural network processes word sequences alongside author identification data to produce author vectors (floating-point or quantized floating-point values). These vectors characterize communication style, personality type, and content selection patterns. For verification, a new document's text is processed to generate a candidate author vector, which is compared against stored author vectors. If the distance falls below a threshold, authorship is confirmed. This enables: (1) verifying claimed authorship, (2) detecting ghostwriting or AI-generated content attributed to a known author, (3) cross-site author identification even without bylines.
Formula: AuthorVector(author) =
  neural_net(
    word_sequences,
    author_id
  ) → float_vector

verify(doc, author) =
  distance(
    AuthorVector(doc),
    stored_vector(author)
  ) < threshold
// Low distance = confirmed authorship
// High distance = style mismatch (possible AI/ghost)
Patent: US11275895 (Generating author vectors)
Affects: Affected by: writing style consistency across publications. Sudden shifts to AI-generated prose increase vector distance from established author profile.

### Author Peer Reputation (Patent)
Computes author reputation from three components: (1) peer review scores from other authenticated authors in the same domain, (2) an authentication score based on identity verification strength, and (3) per-topic reputation that tracks expertise across specific subject areas. Authors accumulate reputation over time across publications, with topic-specific granularity allowing an author to be authoritative in one domain but not another.
Formula: AuthorReputation(author) = combine(
peer_review_score(author, topic),
auth_score(identity_verification),
topic_reputation[t1, t2, ..., tN]
)  // per-topic granularity
// peer reviews from authenticated authors
// accumulates across publications over time
Patents: US8150842 (reputation of an author of online content)
Affects: Affected by: peer endorsements, identity verification strength, topic-specific publication history. Authors with verified identities and peer recognition score higher.

## Pipeline
- **Author Detection**: isAuthor boolean: Google detects whether a page has distinct authorship through bylines, structured data (Person schema), author pages, and content patterns.
- **Entity Resolution (Webref)**: Detected author matched against Google's entity graph. Name disambiguation, cross-site matching, and entity consolidation performed.
- **GAIA Identity Linking**: authorObfuscatedGaiaStr links resolved author entity to Google's authentication system. Enables cross-service identity verification.
- **Reputation Aggregation**: Author's publication history, topic consistency, and quality metrics aggregated across all resolved content.
- **Quality Integration**: Author quality signals feed into Q* and NSR quality scoring. Amplified for YMYL topics where author credentials are critical.

## Timeline
- 2005: Expert Identification - US7536382: Methods for identifying subject matter experts based on content analysis and citation patterns.
- 2010: Author Peer Reputation (US8150842) - Patent on computing author reputation from peer reviews, authentication scores, and per-topic reputation. Authors accumulate topic-specific expertise scores across publications. Peer endorsements from authenticated authors carry more weight.
- 2011: Google Authorship Launch - Google launches rel=author markup and Google+ integration. Author photos appear in search results.
- 2014: Authorship Markup Discontinued - Google removes author photos from results and deprecates rel=author. But internal authorship signals continue (isAuthor persists in API).
- 2018: Author Entity Resolution - US10534810: Advanced author entity resolution across different sites and publications. Authorship becomes an entity problem.
- 2020: Author Vectors Patent - US11275895: Generating Author Vectors. Neural network creates writing-style fingerprints per author. Can verify authorship and detect style shifts (including AI-generated content attributed to human authors).
- 2022: E-E-A-T Update - Google adds 'Experience' to E-A-T guidelines. Author first-hand experience becomes a quality criterion for raters.
- 2024: GAIA Link Revealed - API leak exposes isAuthor (100/100 corroboration) and authorObfuscatedGaiaStr. Author identity linked to Google's authentication system.
- 2024: Authorship System Confirmed - DOJ trial documents confirm Authorship as a separate system from Entity. Dual attribution suggests a detection-then-evaluation pipeline.
