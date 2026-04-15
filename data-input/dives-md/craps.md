# CRAPS: Chrome Realtime Aggregated Page Signals

## Key Insight
CRAPS is not a standalone ranking system. It is the data processing layer between raw user behavior (Chrome visits, clicks, scrolls, purchases) and the ranking signals that Ascorer consumes. The DOJ confirmed it as a core system within NavBoost (QualityNavboostCrapsCrapsData module). The API leak revealed CRAPS operates at three patternLevels: Level 0 (exact URL), Level 1 (host), Level 2 (domain), meaning click signals propagate upward from individual pages to entire domains. The StatsWithWeightsProto model (15 statistical attributes) measures click quality consistency across all URLs in a domain, not just averages. A domain with high mean engagement but high variance scores worse than one with moderate but consistent engagement.

### Signals, Core (doj)
- doj:craps - Click and Results Prediction System. Processes aggregated click data into ranking adjustments and demotions. Core integration point for weighted user signals within NavBoost. Module: QualityNavboostCrapsCrapsData. Confirmed as core system (PXR0357, Nayak interview; UPX0197, Grushetsky)
- crapsNewUrlSignals - Encoded URL-level click and impression data within the CRAPS system for per-page behavioral scoring. Solves the cold-start problem: fresh URLs without historical NavBoost data receive immediate engagement metrics from Chrome visits (DOJ: doj:hobo-mapping-updates)
- crapsNewHostSignals - Encoded host-level click and impression data within the CRAPS system for domain-level behavioral scoring. Aggregates Chrome browsing behavior across an entire domain (DOJ: doj:hobo-mapping-updates)
- Chrome data feeds ranking - Form submissions (chromeFormSubmissions), scrolling intensity (chromeScrollingIntensity), purchase activity (chromePurchaseActivity), unique views (uniqueChromeViews), total Chrome integration (chromeInTotal), and transition clicks (chrome_trans_clicks) all feed the CRAPS processing pipeline
- CRAPS predictions listed as core popularity signal - Infrastructure documentation confirms CRAPS predictions as a primary component of the popularity signal pipeline alongside NavBoost click aggregation

### CrapsClickSignals (10 Attributes) (leak)
- squashedClicks / unsquashedClicks - Click counts with and without squashing (deduplication/capping per user). Squashing prevents a single user's repeated clicks from inflating a URL's score
- squashedImpressions / unsquashedImpressions - Impression counts (SERP appearances) with and without user-level squashing. The denominator for CTR-derived signals
- goodClicks - Positive engagement clicks (long dwell time, task completion). The primary positive signal in click classification
- badClicks - Negative signals (quick bounces, pogo-sticking back to SERP). Pogo-sticking is the strongest negative signal
- lastLongestClicks - Session's longest click duration. Strongest individual satisfaction proxy: the final result a user spent significant time on before ending their session
- clickDissatisfaction - Penalty metric for users who continue searching after clicking a result. Quantifies the gap between expected and actual satisfaction

### CrapsData (22 Attributes) (leak)
- patternLevel - URL abstraction hierarchy for signal inheritance. Level 0: full URL (exact page). Level 1: host pattern (p://example.com). Level 2: domain pattern (p://def.ghi). Click signals propagate from pages up to domain level
- sliceTag - Flexible segmentation key for ad-hoc signal slicing by device, country, locale, or custom dimensions. Enables arbitrary experimental signal cuts
- mobileSignals - Separate CrapsClickSignals instance from tier 1/2 mobile interfaces in QSessions. Not just device slicing but independent mobile click signal computation
- CrapsDevice.uxTier - Browser capability tier: 1 = modern browsers, 2 = middle tier, 3 = very old browsers. Disproportionate low-tier clicks may flag automated manipulation (bots use outdated user agents)
- CrapsDevice.os - Operating system tracked per click session, enabling OS-level signal segmentation
- packedIpAddress - Network byte order IP string used by CrapsIpPrior system (references internal craps-ip-prior.h). Enables IP-level reputation scoring
- unscaledIpPriorBadFraction - IP-level reputation score before linear scaling/offset/min-max transformation (references craps-penalty.cc). IP block reputation may explain shared hosting ranking disadvantages

### StatsWithWeightsProto (15 Attributes) (leak)
- StatsWithWeightsProto - Statistical distribution model measuring click quality consistency across all URLs within a patternLevel (domain/host/URL). Fields: n (sample size), weightedN, mean, median, stddev, variance, varOfMean, stdError, hi, lo, pc10, pc25, pc75, pc90, kind
- Consistency over spikes - A domain with median good-click ratio 0.6 and tight standard deviation signals consistently strong quality. Same mean but massive standard deviation signals inconsistent quality, likely triggering site-wide demotion
- Percentile breakpoints - pc10 (10th percentile) captures worst-performing pages. A domain's weakest pages drag down the entire profile. Best-performing pages alone are insufficient if the bottom 10% performs poorly
- patternSccStats - Links StatsWithWeightsProto to CrapsData, analyzing the distribution of click quality across URLs within each pattern level

### Anti-Manipulation Signals (leak)
- voterTokenCount - Number of distinct voter tokens (lower bound on distinct users). 500 clicks from 3 tokens = red flag; 500 clicks from 480 tokens = healthy organic. Suppressed below minimum user threshold for privacy
- GlueVoterTokenBitmapMessage - Bitmap-based voter deduplication structure with two attributes. Enables compact representation of unique user identifiers for anti-fraud detection
- CrapsIpPrior - IP-level reputation scoring within NavBoost's CRAPS system. packedIpAddress maps to unscaledIpPriorBadFraction, assigning bad-click probability to IP ranges before scaling. Shared hosting on low-reputation IP blocks = ranking drag
- uxTier manipulation detection - Browser capability tier 3 (very old browsers) correlates with bot traffic. Disproportionate tier-3 clicks on a URL triggers scrutiny

### Real-Time CRAPS Signals (leak)
- crapsNewUrlSignals - Chrome Realtime Aggregated Page Signals for new/fresh URLs. Immediate engagement metrics for recently discovered pages that lack historical NavBoost data
- crapsAbsoluteHostSignals - Chrome Realtime Aggregated Page Signals at host level. Real-time site-level quality signals influencing all pages on that host
- crapsNewNsrSignal - Site-level NSR (Normalized Site Rank) recomputed using CRAPS data in near-real-time. Site quality scores update rapidly when Chrome engagement patterns shift
- RealTimeBoost - Re-ranking twiddler incorporating real-time signals including incoming Chrome visits. Applied as post-retrieval boost for content receiving sudden engagement spikes before the full NavBoost pipeline processes the data. Latency: ~10 minutes via InstantGlue
- QualityNavboostCrapsAgingData - Click data segmented into time-based buckets: lastWeekBucket, lastMonthBucket for trend analysis. Enables temporal decay and freshness detection within CRAPS

### 10 Patents (patent)
- Click-based ranking: ranking documents based on aggregated click data patterns over time (US8661029)
- Behavioral ranking modification: modifying search result ranking based on implicit user feedback signals including view duration and click patterns (US8874555)
- Rapid behavior-based index updates: propagating user behavior signals to index in near-real-time rather than batch processing (US8244701)
- User behavior feature ranking: ranking using click probability models trained on combined user behavior and document feature data (US7716225)
- Implicit feedback ranking: long clicks vs short clicks as relevance signals, modifying ranking based on view duration (US11816114)
- Site quality via dwell time: measuring pogo-sticking and dwell time patterns for site-level quality assessment (US9195944)
- Category-normalized dwell time: evaluating dwell time relative to content category peers, not globally. Direct URL inputs get a 1.5x duration boost (US9514194)
- Automated satisfaction measurement: systematic evaluation of user satisfaction from search result interactions (US7937340)
- Click prediction unification: Eric Lehman's 'Predictionosaurus Clickus' system for predicting which results users will click and boosting those results (US9684697)
- Document classification from user inputs: classifying documents based on aggregated user interaction patterns rather than content analysis alone (US7769751)

### Signal Hierarchy and Propagation (exploit)
- Three-level signal propagation: CRAPS computes signals at URL level (patternLevel 0), then aggregates to host level (1), then domain level (2). A strong page lifts its host; a weak page drags down its domain. This is how a few low-quality pages can demote an entire site
- Consistency beats spikes: StatsWithWeightsProto measures variance, not just mean. A domain where 90% of pages have strong engagement but 10% have high bounce rates will score worse than a domain with uniformly moderate engagement. The pc10 (10th percentile) is the most dangerous metric: your worst pages define your floor
- IP reputation as hidden factor: CrapsIpPrior assigns a bad-click fraction to IP ranges. Sites on shared hosting with low-reputation IP neighbors inherit a ranking penalty. This explains why dedicated hosting or CDN migration sometimes produces unexplained ranking improvements
- Mobile signals are independently computed: mobileSignals in CrapsData is a separate CrapsClickSignals instance, not a filter on desktop data. Mobile and desktop ranking signals diverge at the CRAPS level, confirming that mobile-first indexing uses genuinely different behavioral data
- Bot detection via browser tier: CrapsDevice.uxTier classifies browsers into 3 tiers. Click manipulation using outdated browser signatures (tier 3) is trivially detectable. Sophisticated click manipulation must use modern browser fingerprints (tier 1) and diverse voterTokens

### Cold-Start and Freshness (exploit)
- Fresh content bypasses NavBoost via CRAPS: crapsNewUrlSignals provides immediate engagement metrics for pages without historical NavBoost data. Combined with RealTimeBoost twiddler (~10 min latency via InstantGlue), fresh content from high-engagement domains can rank within minutes of publishing
- NSR updates in near-real-time: crapsNewNsrSignal recomputes Normalized Site Rank using live CRAPS data. A viral article can temporarily boost an entire domain's quality score before the batch NSR recomputation runs
- Aging buckets create decay curves: QualityNavboostCrapsAgingData segments clicks into lastWeekBucket and lastMonthBucket. Recent engagement is weighted more heavily than older data, creating a natural decay that requires sustained traffic to maintain rankings
- Chrome visits as the fastest signal path: Direct Chrome browser visits feed CRAPS before any search interaction occurs. A page going viral on social media generates Chrome visit signals that can influence rankings before users even search for related queries

### Calculations
- CRAPS Signal Transform: Transforms raw click data from Glue into structured ranking signals at three hierarchy levels. URL-level signals (patternLevel 0) are aggregated to host (1) and domain (2). Each level computes CrapsClickSignals (goodClicks, badClicks, lastLongestClicks, clickDissatisfaction) plus statistical distributions via StatsWithWeightsProto. Affects: Affected by: all user engagement signals. Click quality, dwell time, pogo-sticking, and session behavior directly determine the ranking modifier applied.
- StatsWithWeightsProto Consistency Score: Computes 15 statistical measures across all URLs within a patternLevel to assess engagement consistency. Domains with tight standard deviation (consistent quality across pages) score better than domains with high variance even at the same mean. The pc10 (10th percentile) captures worst-performing pages. Affects: Affected by: page-level quality variance. Pruning low-performing pages improves domain-wide consistency scores.
- CrapsIpPrior: Assigns a bad-click fraction to IP ranges using packedIpAddress. The unscaledIpPriorBadFraction score undergoes linear scaling, offset, and min-max transformation (craps-penalty.cc) before being applied as a ranking modifier. Sites on IP ranges with historically high bad-click ratios inherit a penalty. Affects: Affected by: hosting environment. Dedicated IP or CDN avoids penalty inheritance from bad neighbors.
- RealTimeBoost: Post-retrieval re-ranking twiddler that applies real-time Chrome visit data and CRAPS signals before the full NavBoost batch pipeline processes. Operates via InstantGlue with ~10 minute latency. Uses crapsNewUrlSignals (URL-level), crapsAbsoluteHostSignals (host-level), and crapsNewNsrSignal (site-level NSR recomputation). Affects: Affected by: real-time engagement velocity. Sudden Chrome visit spikes trigger immediate ranking boosts before batch NavBoost catches up.
- Click Squashing: Normalizes click counts by applying per-user deduplication (squashing). squashedClicks caps the influence of any single user's repeated interactions, while unsquashedClicks retains raw counts for statistical analysis. The ratio squashedClicks/unsquashedClicks itself is an anti-manipulation signal: a low ratio indicates concentrated click sources. Affects: Affected by: user diversity. Organic traffic from diverse users produces healthy squash ratios. Manipulation attempts concentrate clicks from few voters.

### CRAPS in the Ranking Pipeline
- 1. Raw Data Collection (Glue): Glue logs every SERP interaction: query text, location, device, layout, clicks, hovers, scrolls, swipes, dwell time. Chrome browser independently reports visits, form submissions, scrolling intensity, purchase activity. All data tagged with voterTokens and CrapsDevice metadata (uxTier, OS, IP).
- 2. CRAPS Signal Processing: Raw Glue data is transformed into CrapsClickSignals (goodClicks, badClicks, lastLongestClicks, clickDissatisfaction, squashed/unsquashed counts). Signals computed at three patternLevels: URL (0), host (1), domain (2). Segmented by sliceTag (device, country, locale). Mobile signals computed independently.
- 3. Statistical Aggregation (StatsWithWeightsProto): For each patternLevel, CRAPS computes a 15-attribute statistical distribution across all child URLs. Measures mean, median, standard deviation, variance, and percentile breakpoints (pc10 through pc90). This consistency score determines whether a domain's quality is uniform or variable.
- 4. Anti-Manipulation Filtering: voterTokenCount validates user diversity. CrapsIpPrior applies IP-level reputation scoring via craps-ip-prior.h and craps-penalty.cc. CrapsDevice.uxTier flags suspicious browser tier distributions. GlueVoterTokenBitmapMessage provides compact user deduplication. Click squashing caps per-user influence.
- 5. Real-Time Path (InstantGlue → RealTimeBoost): For high-priority content, InstantGlue delivers engagement data with ~10-minute latency. RealTimeBoost twiddler applies crapsNewUrlSignals, crapsAbsoluteHostSignals, and crapsNewNsrSignal as post-retrieval re-ranking before the batch NavBoost pipeline runs. Solves cold-start for fresh URLs.
- 6. Batch Path (NavBoost Table): CRAPS signals feed into the main NavBoost lookup table. Updated on a rolling 13-month window (pre-2017: 18 months). QualityNavboostCrapsAgingData segments into temporal buckets (lastWeekBucket, lastMonthBucket) for trend analysis and decay. This is the primary path for established content.
- 7. Ranking Integration (Ascorer): CRAPS-derived signals are consumed by Ascorer as part of the ABC framework (Authority-Body-Clicks). Click signals (C component) combine with topicality (T*), quality (Q*), and authority to produce final IR scores. CRAPS predictions are listed as a core popularity signal in the infrastructure pipeline.

### Timeline
- 2003 (patent): Early Click Ranking Patents - US7716225 filed: ranking documents using click probability models trained on user behavior and feature data. Foundation for behavioral ranking at Google.
- 2005 (event): NavBoost Created by HJ Kim - NavBoost created as HJ Kim's second signal project (PXR0356). Initially a simple click aggregation table that would grow to become 'more powerful than the rest of ranking combined' (Grushetsky, UPX0197).
- 2008 (patent): Click-Based Ranking Formalized - US8661029 filed: ranking based on aggregated click data patterns over time. US7937340 granted: automated satisfaction measurement for web search.
- 2010 (patent): Rapid Behavior Updates - US8244701: rapid behavior-based index updates enabling near-real-time signal propagation. Foundation for the InstantGlue and RealTimeBoost pipeline.
- 2014 (doj): Mobile NavBoost Launched - Separate mobile click signal system launched Q1 2014 producing a 'huge LE win' (UPX1087, Ranking Newsletter Aug 2014). Confirmed mobile and desktop search intents diverge, leading to independent CRAPS mobile signal computation.
- 2016 (doj): Click Data Scale Revealed - Eric Lehman (UPX0192): ~1 billion new user behavior examples per day. Training corpus: ~100 billion clicks vs ~1 million IS rater scores. Multiple click prediction systems nicknamed 'Predictionosaurus Clickus'.
- 2019 (doj): NavBoost Dominance Acknowledged - VP Grushetsky (UPX0197): 'NavBoost alone was likely more powerful than the rest of ranking combined.' Engineers outside NavBoost team resented its dominance and the fact it was 'stealing wins'.
- 2023 (doj): DOJ Trial Confirms CRAPS as Core System - Pandu Nayak testimony (PXR0357) confirms NavBoost/CRAPS as core ranking system. Judge Mehta rules: 13 months of Google data = 17.5 years of Bing data. NavBoost beats LLMs on freshness (FOF 103). CRAPS confirmed in QualityNavboostCrapsCrapsData module.
- 2024 (leak): API Leak Exposes CRAPS Architecture - API leak reveals six NavBoost protobuf models: CrapsClickSignals (10 attrs), CrapsData (22 attrs), CrapsDevice, FeatureCrapsData, StatsWithWeightsProto (15 attrs), GlueVoterTokenBitmapMessage. Full CRAPS data architecture exposed including patternLevel hierarchy, IP reputation, and anti-manipulation systems.
- 2024 (exploit): Click Probability Confirmed via Bounty - Candour exploit: 2TB data across 90M queries confirmed per-result click probability exists for every organic result. Confirmed via $13,337 Google Vulnerability Reward Program bounty to Mark Williams-Cook (Candour Agency, Dec 2024).
- 2024 (doj): NavBoost Feeds LLM Pre-Training - PXR0095 (Gemini v3): 'Append anonymized NavBoost Queries for selected documents' as pre-training input for Search-dedicated Gemini model. CRAPS/NavBoost data now feeds both traditional ranking AND LLM quality improvement.

### Contradictions
- Public (Google Search Central Documentation): Google does not use Chrome data for ranking web search results. vs Internal (API Leak: CRAPS signals (crapsNewUrlSignals, crapsAbsoluteHostSignals, chromeInTotal, uniqueChromeViews, chromeFormSubmissions, chromeScrollingIntensity, chromePurchaseActivity)): Six distinct Chrome-derived signals feed directly into CRAPS for ranking. Chrome visits are the fastest signal path (~10 min via InstantGlue). The 'Chrome Realtime Aggregated Page Signals' name explicitly confirms Chrome as a data source for ranking.
- Public (Google Public Statements): Click-through rate is not used as a ranking signal because it is too noisy and easily manipulated. vs Internal (DOJ: Lehman UPX0192 + Nayak PXR0357 + Grushetsky UPX0197): CRAPS processes click data achieving 91% accuracy improvements. Multiple anti-manipulation systems (voterTokenCount, click squashing, CrapsIpPrior, uxTier detection) address noise and manipulation. Google's own VP called it 'more powerful than the rest of ranking combined.'
- Public (Google Search Central): All pages are evaluated independently based on their own content quality and relevance. vs Internal (API Leak: patternLevel hierarchy + StatsWithWeightsProto): CRAPS propagates click signals from URL level (0) to host level (1) to domain level (2). StatsWithWeightsProto computes domain-wide statistical distributions. A domain's pc10 (worst 10% of pages) drags down the entire site's ranking profile. Pages are not evaluated independently.
- Public (Google on Hosting): Hosting provider does not affect search rankings. vs Internal (API Leak: CrapsIpPrior (packedIpAddress, unscaledIpPriorBadFraction, craps-penalty.cc)): CrapsIpPrior assigns bad-click fractions to IP ranges. Sites on shared hosting with low-reputation IP neighbors inherit a ranking penalty applied via craps-penalty.cc. Hosting environment directly affects ranking through IP reputation inheritance.

### Overview
CRAPS is Google's real-time behavioral signal processing system that transforms raw Chrome browser data and click interactions into structured ranking signals. The API leak exposed six protobuf models (CrapsClickSignals, CrapsData, CrapsDevice, FeatureCrapsData, StatsWithWeightsProto, GlueVoterTokenBitmapMessage) with 50+ attributes. CRAPS operates at three hierarchy levels (URL, host, domain), segments signals by device, geography, and browser tier, and feeds both NavBoost's lookup table and the RealTimeBoost twiddler with ~10-minute latency.
