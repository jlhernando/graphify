# Google Discover: Interest-Based Feed Ranking

## Key Insight
Discover represents a fundamentally different ranking paradigm: interest-based rather than query-based. It uses TopicLayer (added to Knowledge Graph) to develop hundreds of thousands of subtopics per topic. A Bayesian CTR prediction model applies learnings at site and subfolder level. As of February 2026, Discover uses a separate algorithm from search, prioritizing quality signals and topic authority over engagement metrics.

### 5 Patents (patent)
- TopicLayer analyzes all web content per topic and develops subtopics
- Identifies both evergreen and fresh content per subtopic
- Entity mapping from Knowledge Graph to user interests
- Patent US9547823: Knowledge graph media recommendations
- Nine-stage pipeline: qualification > interest matching > CTR prediction > filtering
- US7668823: Identifying inadequate search content; US8037063: Identifying inadequate search content; US20100138421: Identifying inadequate search content
- US8458193: System and method for determining active topics

### Signals Confirmed (leak)
- unsquashedClicks - Raw click data before normalization
- clicksTotalInterval - Total clicks within a specific time period
- impressions - Content impression counts in Discover feed
- NavBoost-style good/bad clicks (long dwell vs. pogo-sticking)
- Repeat visit signals for content quality scoring

### DOJ Trial Evidence (doj)
- Pandu Nayak: Discover feed uses NavBoost click signals and Q* quality scores to filter content eligibility
- Content quality gates - Sites below Q* threshold (0.4) excluded from Discover, same gate that blocks featured snippets
- Chrome data feeds Discover - chromeInTotal browsing data and Chrome activity signals used to model user interests for feed personalization
- siteAuthority dependency - Discover eligibility correlates with site-level trust scores; low-authority sites rarely surface in feed

### Optimization Signals (exploit)
- Bayesian CTR prediction at site and subfolder level
- More quality content over time increases feature likelihood
- Topic authority stronger than raw engagement metrics (post-Feb 2026)
- Entity-rich content maps to Knowledge Graph for interest matching
- Large, high-quality images improve Discover card engagement
- E-E-A-T signals weight heavily in Discover quality filtering

### Discover Content Score (Research)
Discover evaluates content through a nine-stage pipeline that combines topic relevance, content quality, user interest alignment, and predicted engagement. Content must pass quality gates before CTR prediction models determine surfacing likelihood.
Formula: DiscoverScore(content, user) =
  TopicMatch(entities, user_interests) *
  QualityGate(E-E-A-T, site_authority) *
  BayesianCTR(site, subfolder)
// separate from search ranking since Feb 2026
Patents: US9547823 (knowledge graph recommendations)
Affects: Affected by: topic authority, content freshness, entity richness, site quality history, user interest profile

### TopicLayer Matching (Research)
TopicLayer extends the Knowledge Graph with topic hierarchies and subtopics. Content is mapped to topic nodes based on entity extraction. User interests are modeled as weighted topic vectors updated from browsing history and engagement patterns.
Formula: TopicMatch(content, user) =
  cosine_sim(
    entity_vector(content_entities),
    interest_vector(user_topics)
  )
// hundreds of thousands of subtopics per topic
Patents: US9547823 (knowledge graph recommendations)
Affects: Affected by: entity extraction quality, Knowledge Graph coverage, user interest freshness

## Pipeline
- **Content Qualification**: Content must meet E-E-A-T and quality thresholds to enter Discover pool
- **Entity Extraction**: Webref extracts entities and maps to Knowledge Graph topics
- **TopicLayer Matching**: Content topics matched against user interest profiles
- **Bayesian CTR Prediction**: Site and subfolder-level CTR prediction based on historical performance
- **Quality Filtering**: NavBoost-style engagement signals filter low-quality content
- **Feed Assembly**: Final ranking and card presentation in user's Discover feed

## Timeline
- 2017: Google Feed Launched - Initial feed feature using basic topic following and trending content.
- 2018: Rebranded to Discover - Google Feed becomes Discover. TopicLayer added to Knowledge Graph for subtopic modeling.
- 2024: API Leak Signals - Leak reveals unsquashedClicks, clicksTotalInterval, and impression tracking for Discover.
- 2026: Separate Algorithm - Discover now ranks independently from search, using quality signals and topic authority over engagement.
