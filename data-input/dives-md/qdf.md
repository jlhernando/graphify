# QDF (Query Deserves Freshness): Time-Sensitive Query Detection

## Key Insight
QDF works by monitoring search volume spikes for individual queries. When a query's search volume deviates significantly from its historical baseline, QDF classifies it as deserving freshness. This triggers three response types: breaking news (sudden spike, never seen before), trending topics (gradual increase indicating growing interest), and recurring events (predictable spikes matching historical patterns like elections or sports events). The classification feeds into the FreshnessTwiddler which re-ranks results to promote recent content, and into Instant Glue which adjusts SERP composition within 10 minutes. QDF is not a static flag; it operates on a sliding scale where the freshness boost intensity matches the query's deviation from baseline volume.

### 4 Patents (patent)
- US8924379: Core QDF patent covering detection of queries that deserve fresh results based on temporal signals and search volume analysis.
- QDF boosting formula - Patent US8832088 (Chen, Zhu, 2012): S'=S*Q^D. Q=query freshness value derived from 8 signals (volume spikes, blog/news/social mentions, click ratios). D=resource freshness value using reverse sigmoid of document age multiplied by provider quality and topicality. QtoA ratio (recent/overall impression probability) detects freshness-seeking queries
- QDF demotion dampening - Patent US9189526 (Chen, Zhu, 2013): Continuation adding protection for high-quality evergreen content. When F(age) is negative, J(query, resource) dampening function prevents popular documents from false demotion. Sigmoidal transformation for smooth score transitions
- Three query freshness categories: breaking (sudden volume spike), trending (gradual increase), recurring (predictable seasonal/event patterns).
- Volume deviation model: query search volume compared against historical baseline. Significant positive deviation triggers QDF classification.
- Sliding scale freshness boost: the intensity of the freshness ranking adjustment matches the magnitude of the volume deviation. A 10x spike gets a stronger boost than a 2x increase.
- Temporal decay modeling: QDF classification expires as the query volume returns to baseline. Breaking news queries lose freshness classification within hours/days.
- US8990241: System and method for recommending queries related to trending topics based on a received query

### Related Leak Evidence (leak)
- No direct QDF signals in the API leak, but the freshness system it feeds is well-documented:
- FreshnessTwiddler: The re-ranking function triggered by QDF. Boosts fresh documents and demotes stale content for time-sensitive queries.
- timeSensitivity: Query-level flag indicating the query needs time-sensitive treatment. Likely set by QDF classification.
- isHotdoc (DOJ): Boolean flag for trending content receiving freshness boost. QDF determines which queries trigger hotdoc detection.
- freshdocs: Link value multiplier favoring newer source pages. More impactful when QDF is active for a query.

### Related DOJ Context (doj)
- No direct QDF signals in DOJ trial evidence, but related freshness mechanisms were confirmed:
- Instant Glue: Real-time signal pipeline with ~10-minute latency on 24-hour data windows. QDF classification triggers Instant Glue to rapidly adjust SERP composition for breaking queries.
- isHotdoc: DOJ-confirmed boolean flag marking trending content. QDF likely determines which queries activate hotdoc detection for their results.
- DOJ testimony confirmed that Google's freshness systems can promote fresh content and suppress stale results within minutes for breaking news events.

### How QDF Affects Your SEO Strategy (exploit)
- Publish first on breaking topics: When QDF activates for a query, recently published content gets a significant ranking boost. Speed of publication matters more than domain authority during QDF windows. Set up monitoring for trending topics in your niche and have rapid publishing workflows ready.
- Understand QDF expiration: QDF freshness boosts are temporary. Once search volume returns to baseline, the query reverts to normal ranking. Content that ranked #1 during a news cycle may drop to page 3 once QDF expires. Build evergreen value alongside timely coverage.
- Target recurring events proactively: QDF detects predictable volume spikes (elections, sports seasons, annual events). Publish comprehensive content BEFORE these events and update it as they happen. Being the first established, high-quality page when QDF activates gives you both freshness and authority signals.
- Monitor search volume spikes in your niche: Tools like Google Trends show when topics are spiking. A spike indicates QDF is likely active for those queries, creating a window where fresh content can outrank established pages regardless of domain authority.
- Update existing content during QDF windows: If you have an established page on a topic that goes trending, updating it with fresh information triggers the significantUpdate signal. This combines your existing authority with freshness, a powerful combination when QDF is active.
- News sitemap and rapid indexing: Content published during QDF windows must be indexed quickly to benefit. Use Google News sitemaps, IndexNow, or the URL Inspection API to accelerate crawling. A page published but not yet indexed misses the QDF window entirely.

### QDF Query Classification (Patent)
QDF monitors real-time search volume for every query and compares it against a historical baseline. When the current volume deviates significantly above the baseline, the query is classified as deserving freshness. The deviation magnitude determines the intensity of the freshness boost applied by downstream systems.
Formula: QDF(query) =
  deviation(
    current_volume(query),
    baseline_volume(query)
  )
if deviation > threshold:
  freshness_boost = scale(deviation)
  // breaking: sudden spike, 10x+ baseline
  // trending: gradual increase, 2-5x baseline
  // recurring: matches historical event pattern
Patents: US8924379 (freshness signals in ranking), US8583617 (temporal query handling), US8832088 (QDF boosting formula S'=S*Q^D), US9189526 (demotion dampening)
Affects: Affected by: real-time search volume, historical query patterns, news cycle events, seasonal trends, social media amplification driving search volume.

### QDF to FreshnessTwiddler (Patent+Leak)
When QDF classifies a query as deserving freshness, it activates the FreshnessTwiddler in the ranking pipeline. The FreshnessTwiddler applies a time-decay function to document scores: recently published or updated content receives a boost proportional to the QDF deviation score, while older content is demoted. Instant Glue provides real-time engagement data within 10 minutes to further refine which fresh content deserves promotion.
Formula: FreshnessTwiddler(doc, query) =
if QDF(query).active:
  score *= freshness_decay(
    doc.age,
    QDF(query).intensity
  )
  // recent docs boosted, stale docs demoted
  // Instant Glue refines within 10 min
Patents: US8924379 (freshness signals)
Affects: Affected by: content publication date, last significant update, QDF intensity level, Instant Glue engagement signals.

## Pipeline
- **Volume Monitoring**: Real-time search volume tracked for all queries. Compared against historical baselines to detect deviations.
- **QDF Classification**: Queries with significant volume spikes classified as breaking, trending, or recurring. Freshness intensity calculated.
- **Query Annotation**: QDF classification attached to query in QUS pipeline. timeSensitivity flag set for downstream systems.
- **FreshnessTwiddler Activation**: Post-ranking re-ranking function boosts fresh content and demotes stale results proportional to QDF intensity.
- **Instant Glue Feedback**: Real-time 24-hour engagement data with 10-minute latency refines which fresh content users are engaging with.
- **Tangram SERP Adjustment**: SERP layout adjusted for freshness queries: news carousels added, Top Stories promoted, stale features suppressed.

## Timeline
- 2007: QDF Algorithm Patented - Google patents Query Deserves Freshness algorithm to detect queries needing recent results based on search volume spikes and temporal patterns.
- 2011: Freshness Update (35% Impact) - Google rolls out major freshness algorithm update affecting 35% of searches. QDF detection expanded to cover trending topics and recurring events.
- 2015: Real-Time Freshness Signals - QDF integrated with Instant Glue for real-time engagement feedback. Fresh content ranking adjustments now happen within minutes, not hours.
- 2024: FreshnessTwiddler Confirmed - API leak confirms FreshnessTwiddler, timeSensitivity, and freshdocs signals that QDF feeds into. The downstream freshness system is well-documented even though QDF itself is not directly named.
