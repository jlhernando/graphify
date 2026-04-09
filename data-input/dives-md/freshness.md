# Freshness: How Google Scores Content Timeliness

## Key Insight
Google tracks the last 20 versions of every document via urlHistory, each with associated weights. The system distinguishes significant updates from minor changes (significantUpdate signal). The freshdocs multiplier means links from newer pages pass more value than links from old pages, creating a freshness cascade through the link graph.

### Signals Confirmed (leak)
- freshdocs - Link value multiplier favoring newer source pages
- FreshnessTwiddler - Re-ranking function boosting fresh documents
- FS - Freshness Score based on update frequency and publish date
- significantUpdate - Flag for major content revisions vs minor changes
- urlHistory - Tracks last 20 document versions with weights
- changeRate - Content change frequency over crawl intervals
- timeSensitivity - Flag for queries needing time-sensitive treatment

### Signals Confirmed (doj)
- freshboxArticleScores - Timeliness scoring for article content
- semanticDateInfo - Semantic extraction of publication/update dates
- lastSignificantUpdate - Timestamp of meaningful content changes
- isHotdoc - Boolean flag for trending content receiving freshness boost

### 16 Patents (patent)
- bylineDate - Date from visible byline in search snippets
- syntacticDate - Date extracted from URL patterns or title
- semanticDate - Publication date inferred from content analysis
- Three independent date extraction methods cross-referenced
- QDF implementation - Patent US8832088 (Chen, Zhu, filed 2012): Query Deserves Freshness algorithm. Computes QtoA ratio (recent impression probability / overall impression probability) to detect queries where users prefer newer content. Boosting formula: S'=S*Q^D where Q=query freshness value, D=resource freshness value. D uses reverse sigmoid of document age multiplied by provider quality and topicality scores. 8 freshness signal inputs including query volume spikes, blog mentions, news mentions, social mentions, news click ratios
- QDF with demotion dampening - Patent US9189526 (Chen, Zhu, filed 2013): Continuation patent adding demotion dampening for popular documents. When F(age) is negative (old content), applies J(query, resource) dampening function to reduce false demotions of high-quality evergreen pages. Transformation functions: linear L(x)=a+bx and sigmoidal S(x)=raise+magnitude/(1+e^(-slope*(x-mid)))
- Content decay modeling for evergreen vs time-sensitive content
- Document inception date scoring - Patent US8521749 (Haahr, Cutts, Dean, Henzinger et al., filed 2003): Foundational freshness patent. Scores documents based on inception date, link growth rate since inception (H=L/log(F+2)), content change frequency/amount, anchor text evolution, traffic trends, user behavior patterns, domain registration data, and ranking history. Multiple inception date signals cross-referenced: first crawl date, first link discovered, domain registration, first reference in external documents.
- Link freshness weighting - US8521749 describes weighting links by the freshness of the linking document and anchor text. A link from a recently updated, high-trust document carries more weight than one from a stale page. Links weighted by trust of source document and authority.
- Spam detection via link growth spikes - US8521749 detects spam through anomalous link growth patterns: large spikes in back links signal either topical phenomena (breaking news) or spam attempts. System applies hysteresis to cap rank growth rate and checks for mentions in news/discussion groups to distinguish legitimate spikes from manipulation.
- Link-based freshness inference - Patent US20050144193 (Henzinger, Google, filed 2004): Infers document freshness by analyzing the freshness of documents linking to it. If the majority of linking documents have recent HTTP last-modified-since headers, the target is scored as fresh. Tracks temporal link existence patterns: link creation/removal rates indicate freshness decay independent of the target document's own modification date
- US20100325114: Systems and methods for determining document freshness; US20070094255: Document scoring based on link-based criteria; US20070100817: Document scoring based on document content update
- US20110029542: Document scoring based on document inception date; US7568148: Methods and apparatus for clustering news content; US8407231: Document scoring based on link-based criteria
- US9477714: Methods and apparatus for ranking documents; US7702618: Information retrieval system for archiving multiple document versions; US7672937: Temporal targeting of advertisements
- US7421432: Hypertext browser assistant; US6493731: Document management system for recording and viewing the history of document use; US8131717: Scoring documents in a database

### How Freshness Scoring Affects Your SEO (exploit)
- Make substantive updates, not cosmetic date changes: Google tracks 20 document versions via urlHistory and uses significantUpdate to distinguish real revisions from trivial edits. Changing the publication date or rewording a sentence does not trigger a freshness boost. Add new sections, update statistics, include recent examples, or rewrite outdated paragraphs for the update to register.
- Publish first on trending topics to capture isHotdoc: This boolean flag marks trending content for a freshness boost. It is triggered by Instant Glue's real-time pipeline, which operates with only 10-minute latency. Being the first authoritative source on a breaking topic earns the hotdoc flag and associated ranking boost. Build editorial workflows that enable rapid, quality publishing on predictable events in your niche.
- Use three date signals consistently and honestly: Google cross-references bylineDate (visible date), syntacticDate (URL/title patterns), and semanticDate (content analysis). Mismatches between these signals erode trust. Display clear, accurate publication and modification dates. Use datePublished and dateModified in Schema.org Article markup. Never manipulate dates to appear fresher than reality.
- Earn links from fresh content for the freshdocs multiplier: Links from newer pages pass more value than links from old, stale pages. This means a mention in a recently published article or news story carries more link equity than a link sitting on a years-old resource page. Target link building from recently published content, press coverage, and current roundups.
- Match your update cadence to your content's decay curve: The changeRate signal tracks how frequently your content changes across crawl intervals. For topics where freshness matters (news, trends, statistics), establish a regular update schedule that Google's crawlers can learn. This teaches the freshness system that your content is actively maintained and worth re-crawling frequently.
- Identify which queries trigger timeSensitivity: Not all queries require fresh content. The timeSensitivity flag marks queries needing time-sensitive treatment. For evergreen queries, freshness matters less than depth and authority. For time-sensitive queries, freshness dominates. Analyze your target queries in Search Console: if rankings fluctuate rapidly, the query likely has timeSensitivity enabled.
- Link growth rate matters more than total links for new content: Patent US8521749 scores documents using H=L/log(F+2), where L is link score and F is time since inception. A new page with 10 links gained in one week scores higher than an old page with 100 links over 10 years. Focus link building efforts on the first weeks after publishing new content for maximum freshness signal impact.
- Register domains for multiple years: US8521749 explicitly states that domains paid for several years in advance are more likely legitimate, while doorway/spam domains are rarely renewed beyond one year. Multi-year domain registration is a positive trust signal. Frequent nameserver changes and DNS record instability are red flags.
- Content update substance is weighted, not just frequency: The patent describes update amount (UA) as ignoring changes to JavaScript, comments, ads, navigation, boilerplate, and date tags. Only substantive content changes (especially to titles and anchor text) count. This aligns with the API leak's significantUpdate signal. Changing a date tag is not an update.
- Yandex cross-reference: 10-year content decay and time-of-day ranking: The Yandex leak reveals a 10-year hard threshold where content stops being favored, and FI_DATER_AGE (-0.207 weight) as one of the heaviest negative factors in their entire system. This cross-validates Google's inception date scoring and suggests evergreen content may face a similar age-based decay curve. Additionally, Yandex applies time-of-day query weighting (different morning vs evening algorithms), a temporal dimension not documented in Google's QDF but plausible given query intent shifts throughout the day.

### lastSignificantUpdate (DOJ: meaningful content change tracking)
Tracks meaningful content changes, distinguishing significant updates from trivial edits. Not just any modification. Google identifies whether the update adds substantive new content or is just cosmetic.
Formula: lastSignificantUpdate(url) = diff(
  urlHistory[last_20_versions]
)  // tracks last 20 document versions
if delta > significance_threshold: update = significant
// cosmetic edits (date changes, rewording) ignored
Patents: US8924379 (freshness signals in ranking), US8583617 (temporal query handling)
Affects: Affected by: making substantive content updates. Changing a date or adding a sentence may not register. Adding new sections, updating data, or significantly rewriting content does.

### isHotdoc (DOJ: Boolean flag for trending content)
Boolean flag (true/false). Marks content that is trending and timely. Receives a freshness ranking boost. Likely triggered by Instant Glue's real-time user signal pipeline.
Formula: isHotdoc(url) = bool(
  InstantGlue.trending(url)
)  // true/false, binary flag
if true: apply freshness_boost
// triggered by real-time user signal spikes
Patents: US8924379 (freshness signals), US8583617 (temporal queries)
Affects: Affected by: publishing timely content on trending topics. Speed matters. First to publish on a breaking topic gets the hotdoc flag.

### semanticDateInfo (DOJ: semantic date extraction)
Semantic extraction of publication and update dates from page content. Google doesn't just read meta tags. It extracts dates from visible content, bylines, and structured data.
Formula: semanticDateInfo(page) = cross_ref(
  bylineDate, syntacticDate, semanticDate
)  // three independent extraction methods
// meta tags, URL patterns, visible content all analyzed
Patents: US8924379 (freshness signals in ranking)
Affects: Affected by: clear, visible publication dates. Use structured data (datePublished, dateModified). Avoid date manipulation. Google cross-references multiple date signals.

### Instant Glue (DOJ: real-time pipeline, ~10min latency)
Real-time variant of Glue delivering fresh user signals with approximately 10-minute latency on 24-hour data windows. Used for breaking news and trending queries. Feeds into Tangram for SERP assembly.
Formula: InstantGlue(query) = stream(
  user_signals, window=24h, latency=~10min
) → Tangram  // real-time SERP assembly
// breaking news and trending queries only
Patents: US8583617 (temporal query handling)
Affects: Affected by: user engagement on fresh content. Quick response to trending topics. Real-time user signals feed back into rankings within minutes.

### Document Inception Date Scoring (Patent US8521749 (Haahr, Cutts, Dean, Henzinger, Lawrence, Pfleger, Tong))
Foundational freshness patent filed in 2003 by Google's earliest search engineers. Scores documents based on their inception date relative to other documents. The system uses multiple methods to determine inception date (first crawl, first link, domain registration, first external reference) and adjusts scores based on link growth rate since inception. A document with 10 links gained yesterday scores higher than a document with 100 links gained over 10 years, because the rate is higher. The formula H=L/log(F+2) adjusts link-based scores by age. Also covers: content update scoring (U=f(UF,UA) where UF=update frequency, UA=update amount), anchor text freshness tracking, traffic pattern monitoring, user behavior analysis (dwell time trends over time), domain legitimacy signals (registration length, DNS record stability), and ranking history hysteresis to cap rank growth rate and detect spam.
Formula: H(doc) = L / log(F + 2)
// L = link-based score, F = time since inception
// Newer docs with fast link growth score higher

ContentUpdate(doc) = f(
  UF,  // update frequency (how often)
  UA   // update amount (how much changed)
)
// UA ignores: JS, comments, ads, nav, boilerplate, dates
// UA weights: title changes, anchor text changes

// Inception date sources (cross-referenced):
// 1. First crawl/index date
// 2. First link discovered
// 3. Domain registration date
// 4. First external reference (news, forums)
Patent: US8521749 (Document scoring based on document inception date). Original filing: US10/748,664 (2003). Co-inventors: Matt Cutts, Jeffrey Dean, Paul Haahr, Monika Henzinger, Steve Lawrence, Karl Pfleger, Simon Tong.
Affects: Affected by: document age, link growth rate, content update frequency and substantiveness, anchor text evolution, traffic trends, domain registration length. Newer documents with rapidly growing natural links benefit. Spam-like link spikes are penalized.

### QDF (Query Deserves Freshness) (Patent US8832088 + US9189526 (Chen, Zhu, Google))
Core freshness boosting algorithm. Determines if a query deserves fresh results by computing QtoA: ratio of recent impression click probability to overall impression click probability. When QtoA > 1, users prefer newer content. The system then boosts fresh documents using S'=S*Q^D, where Q is the query freshness value (derived from 8 freshness signals: query volume spikes, blog/news/social mentions, news click ratios) and D is the resource freshness value (reverse sigmoid of document age, multiplied by provider quality and topicality). Continuation patent US9189526 adds demotion dampening: when F(age) is negative, popular documents are protected from false demotion via J(query, resource) dampening function.
Formula: QtoA = P_recent(click | query) / P_overall(click | query)
// QtoA > 1 → users prefer newer content for this query

Q = Max(QFval, QtoA) + 1  // when QtoA > 1
Q = Max(QFval) - QtoA + 1  // when QtoA ≤ 1

D = F(age) × G(provider_quality) × H(query_provider) × I(topicality)
// F(age) = reverse sigmoid of document age
// When F(age) 

S' = S × QD  // final boosted score
// 8 QFval inputs: query volume, blog, news, social, click ratios
Patents: US8832088 (freshness-based ranking, 2012), US9189526 (freshness with demotion dampening, 2013)
Affects: Affected by: query freshness signals (volume spikes, news/blog/social mentions), document age, provider quality score, topicality alignment. Fresh content on trending queries gets exponential boost. Evergreen content on stable queries protected from false demotion.

### Domain Legitimacy Scoring (Patent US8521749 (Section: Domain-Related Information))
The same patent describes using domain registration data as a freshness and legitimacy signal. Domains paid for several years in advance are more likely legitimate than domains registered for only one year (typical of doorway/spam domains). The system monitors DNS records over time: stable contact information, consistent name servers, and long registration periods indicate legitimacy. 'Bad' name servers are identified by hosting mainly spam, doorway, or bulk domains.
Formula: DomainLegitimacy(domain) = score(
  registration_length,  // years paid ahead
  dns_stability,         // contact/nameserver changes
  nameserver_reputation  // known-good vs known-bad
)
// Valuable domains: paid 5-10 years ahead
// Spam domains: rarely renewed beyond 1 year
// DNS churn: frequent nameserver changes = red flag
Patent: US8521749
Affects: Affected by: domain registration length, DNS record stability, nameserver reputation, contact information consistency. Long-term domain registrations with stable records signal legitimacy.

### Link-Based Freshness Inference (Patent US20050144193 (Henzinger, Google, 2004))
Infers target document freshness by analyzing the modification status of all documents linking to it, rather than relying on the target's own HTTP headers or metadata (which can be manipulated). Counts fresh vs non-fresh documents in the referral set using HTTP last-modified-since headers. If the majority of linking documents are recently modified, the target is scored as fresh. Also tracks temporal link existence: link creation and removal rates over time indicate content relevance decay. A document that stops attracting new links is inferred as stale even if its own content hasn't changed.
Formula: LinkFreshness(doc) =
  count(fresh_referrers) /
  count(all_referrers)
// fresh = recent HTTP last-modified-since

if fresh_ratio > 0.5: Fr = high
else: Fr = low

LinkDecay(doc, t) =
  links_created(t) - links_removed(t)
// declining net links = content relevance decay
Patent: US20050144193 (Document freshness determination, 2004)
Affects: Affected by: freshness of linking documents, link acquisition rate, link removal rate. Documents that continue attracting links from fresh sources maintain freshness scores.

## Pipeline
- **Date Extraction**: bylineDate, syntacticDate, semanticDate
- **Change Detection**: urlHistory (20 versions), significantUpdate, changeRate
- **Freshness Score**: FS composite + isHotdoc trending flag
- **Link Freshness**: freshdocs multiplier on inbound links
- **FreshnessTwiddler**: Final re-ranking boost in Lazy Twiddler stage

## Timeline
- 2004: Link-Based Freshness Inference (US20050144193) - Henzinger patents transitive freshness inference: document freshness determined by analyzing modification status of linking documents rather than the target's own metadata. Majority voting on fresh vs stale referrers. Temporal link existence tracking for freshness decay detection.
- 2003: Document Inception Date Scoring (US8521749) - Foundational freshness patent filed by Haahr, Cutts, Dean, Henzinger et al. Covers: inception date scoring (H=L/log(F+2)), content update scoring (frequency + amount), link growth rate analysis, anchor text freshness, traffic/user behavior monitoring, domain legitimacy (registration length, DNS stability), and ranking history hysteresis for spam detection.
- 2007: Query Deserves Freshness - Google patents QDF algorithm to detect queries that need recent results based on search volume spikes.
- 2011: Freshness Algorithm Update - 35% of searches affected. Fresh content boosted for recent events, recurring events, and frequently updated topics.
- 2012: QDF Implementation Patented (US8832088) - Chen and Zhu patent the core QDF algorithm. S'=S*Q^D boosting formula. QtoA ratio compares recent vs overall click impression probabilities to detect freshness-seeking queries. 8 freshness signal inputs (query volume, blog/news/social mentions, click ratios). Resource value D uses reverse sigmoid of document age multiplied by provider quality and topicality.
- 2013: QDF Demotion Dampening (US9189526) - Continuation patent by Chen and Zhu adds protection against false demotion of high-quality evergreen content. When F(age) is negative, J(query, resource) dampening function prevents popular documents from being incorrectly penalized. Sigmoidal transformation: S(x)=raise+magnitude/(1+e^(-slope*(x-mid))).
- 2015: Content Change Tracking - Patents on distinguishing significant updates from cosmetic changes. urlHistory tracks last 20 document versions.
- 2019: freshdocs Link Multiplier - Link value weighted by freshness of the linking page. Newer pages pass more link equity.
- 2023: Yandex Leak Cross-Reference - Yandex's FI_DATER_AGE (weight: -0.207) is one of the heaviest negative factors, confirming content age as a dominant signal. Yandex applies a 10-year hard decay threshold where old content stops being favored. Time-of-day query weighting (IND_FI_MORNING_QUERY) applies different ranking algorithms morning vs evening, a temporal dimension beyond QDF. News-specific freshness modifiers confirm Google's QDF/isHotdoc pattern.
- 2024: API Leak: 12 Freshness Signals - freshdocs, FreshnessTwiddler, significantUpdate, changeRate, timeSensitivity, urlHistory all confirmed.
- 2024: DOJ Trial: isHotdoc Flag - Boolean trending content flag for freshness boost. lastSignificantUpdate tracks meaningful changes vs minor edits.

## Contradictions
- Public (Google Public): "Just update your content regularly to stay fresh."
  Internal (API Leak): Google tracks exactly how content changes across 20 versions. significantUpdate distinguishes real updates from cosmetic edits. Minor date changes or rewording don't trigger freshness boosts.
- Public (Google Public): "We use the date shown on the page to determine freshness."
  Internal (API Leak): Three independent date systems (bylineDate, syntacticDate, semanticDate) cross-referenced. Google doesn't trust any single date source and infers true publish dates independently.
- Public (Google Public): "All links are treated equally regardless of when they were created."
  Internal (API Leak): freshdocs multiplier explicitly weights link value by source page age. Links from newer pages pass more equity than links from old, stale pages.
