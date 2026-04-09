# NavBoost: Google's Click Signal System

## Key Insight
NavBoost is "just a big table" of click data, not a machine learning system. It aggregates and looks up user interaction statistics. It retains 13 months of click data (pre-2017: 18 months), sliced by location and device type. Pandu Nayak testified it achieves 91% accuracy improvements in ranking quality. Judge Mehta's opinion: 13 months of Google data equals over 17.5 years of Bing data. LLMs did not replace NavBoost in ranking, and NavBoost can beat LLMs on freshness.

### Core NavBoost Signals (leak)
- goodClicks - Positive engagement clicks (long dwell, task completion)
- badClicks - Negative signals (quick bounces, pogo-sticking)
- lastLongestClicks - Session's longest click duration (satisfaction proxy)
- chromeInTotal - Total Chrome browser views at site level
- directFrac - Direct/type-in traffic fraction (brand authority signal)
- RealTimeBoost - Incoming Chrome visits and instant NavBoost for breaking content
- clickDissatisfaction - Penalty for users who keep searching after clicking
- serpDemotion - Pre-computed integer demotion multiplier for SERP-level pogo-sticking (poor title/snippet promise)
- navDemotion - Pre-computed integer demotion multiplier for poor on-page experience after click-through (slow site, confusing navigation)
- QualityNavboostCrapsAgingData - Click data segmented into time-based buckets: lastWeekBucket, lastMonthBucket for trend analysis
- Duration Performance Score - Category-normalized dwell time (Patent US9514194, Navneet Panda). Dwell time evaluated relative to content category peers, not globally. A 5-minute visit on a recipe site is measured against other recipe sites. Direct URL inputs (type-in traffic) get a duration boost (e.g., 1.5x). Last-visited durations discounted to filter short clicks.
- impressions - Denominator for CTR calculation per query-document pair
- absoluteImpressions - Host-level unsquashed impressions operating at domain level, not URL level
- unicornClicks - Click subset associated with "Unicorn user" events (possibly authenticated Google users or Chrome users; undefined in leak)
- voterTokenCount - Number of distinct voter tokens (lower bound on distinct users). Anti-manipulation signal: 500 clicks from 3 tokens = red flag; 500 clicks from 480 tokens = healthy organic. Used for privacy-related filtering; signal suppressed below minimum user threshold

### Signals,  Core (doj)
- NavBoost - 91% accuracy, "just a big table," not ML (Nayak sworn testimony). VP Grushetsky (2019 email, DOJ exhibit UPX0197): "NavBoost alone was likely more powerful than the rest of ranking combined." Created by HJ Kim (his 2nd signal project, many patents, PXR0356). Nayak (PXR0357): NavBoost is a "QD table, a query-to-document lookup table, used in both directions containing counts/frequencies of user query activity by document". Mehta opinion: Pre-2017 trained on 18 months; now 13 months. 'Navboost remains one of the most power[ful] ranking components historically' (UPX190)
- NavBoost beats LLMs on freshness - Mehta opinion (FOF 103): 'Traditional systems like Navboost can also beat out LLMs (and even generalization systems) in certain aspects of SERP production, like freshness.' LLMs did NOT replace NavBoost/QBST in ranking (FOF 102). LLMs are 'additional signals that get balanced both against each other as well as against other signals' (Nayak)
- NavBoost as early-stage filter - Nayak: "You get NavBoost only after they're retrieved in the first place." NOT a final twiddler making minor adjustments. NavBoost is applied after initial retrieval to cull tens of thousands of candidates down to hundreds before passing to RankBrain/DeepRank. Pages need topical relevance and links to enter the candidate set; NavBoost culls and reorders within that set. Cannot rank documents lacking click history (bootstrapping problem for new content)
- QSessions - Structured internal session model tracking query, clicks, reformulations, and session termination. Primary data source for NavBoost click classification
- Position bias correction - Nayak (PXR0357): an early click signal measured show-vs-click rate but was biased by link position. A Google engineer developed a modification that calculates click value while correcting for position bias. This is foundational to how NavBoost weights clicks
- Multi-click = hard query - Eric Lehman (UPX0219, May 2020): "If someone clicks on three search results, which one is bad? Well, likely ALL of them, because it is probably a hard query if they clicked 3 results." Multiple clicks signal query difficulty, not just individual page quality
- Click data scale - ~1 billion new user behavior examples per day (Lehman UPX0192, 2016). Training corpus contains ~100 billion clicks vs ~1 million IS rater scores. Clicks provide 'vastly clearer picture of how people interact with search results'
- Click prediction strategy - Lehman (UPX0192): 'Predict what results users will click. Boost those results. Patch up problems with page quality, relevance, optionalization.' Multiple overlapping click prediction systems nicknamed 'Predictionosaurus Clickus'
- Evaluation: train on past, predict future - Click signal evaluation methodology 'train on the past, predict the future,' largely eliminating overfitting. Continuous evaluation on fresh queries and live index. Importance of freshness and localization built into the metric (Lehman UPX0192)
- RankLab - Internal tuning tool for ranking curves. '1,000,000 IS ratings are more than sufficient to superbly tune curves via RankLab and human judgment' (Lehman UPX0192)
- Glue - SERP interaction logger and underlying data warehouse for NavBoost. Tracks: query text, location, device, layout, clicks, hovers, scrolls, swipes, dwell time. Stores goodClicks, badClicks, lastLongestClicks per query-document pair. Segmented by geographic location and device type
- InstantGlue - Real-time user signals with ~10-minute latency for breaking news
- P* - Popularity score combining NavBoost clicks with anchor metrics
- NavBoostLocationSlice - Click data segmented by geographic location
- NavBoostDeviceSlice - Click data segmented by device type (mobile/desktop)
- MobileNavBoost - Separate mobile click signal system launched Q1 2014 producing a 'huge LE win' (UPX1087, Ranking Newsletter Aug 2014). Confirmed mobile and desktop search intents diverge. Mobile users: higher abandonment, fewer result examinations, skew toward head and local queries
- QBST (Query-Based Salient Terms) - Memorization system trained on 13 months of user data. Identifies words/pairs that 'should appear prominently on web pages relevant to that query' (Lehman). Helps GSE 'understand facts about the world' (Mehta FOF 95). Distinct from NavBoost: QBST memorizes term associations, NavBoost memorizes click patterns
- Chrome data - Form submissions, scrolling intensity, purchase activity feed rankings
- Priors algorithm (UPX0205, Research All-Hands Apr 2015): 'Score the doors based on how many people took it. Rank the choices based on how popular it is.' Described as 'one of the strongest signals for much of Google's search and ads ranking.' At Google's scale, 'even the most obscure choice would have been exercised by thousands of people.' Behavioral similarity matching: describe results by the people who chose them, not demographics
- Click data = 'secret sauce' (UPX0235, Giannandrea Aug 2016): During Apple ISA negotiations, Apple requested click data. Daniel Alegre told Apple: 'we don't share this information as it is key to our secret sauce.' JG (Giannandrea): 'if its which web pages people ultimately visit for a query we would *not* share that ranking signal.' Confirms click destination AND dwell/satisfaction time are ranking signals
- Click tracking infrastructure (UPX2022, Google vs Bing 2017): Google's click tracking uses ~50-100 compressed bytes per URL via onmousedown='return rwt(this,...)' rewrite. Bing used just a few bytes via an 'h' attribute. Direct evidence of Google's heavier click data collection per result
- NavBoost queries as LLM pre-training data (PXR0095, Gemini v3 2024): 'Append anonymized NavBoost Queries for selected documents' as pre-training input for Search-dedicated Gemini model. 'Anon navboost queries could be huge, given we see MMLU increases from aquarium queries.' NavBoost data now feeds both ranking AND LLM quality
- AI Overview clicks are higher quality (PXR0038): 'When people click links from AI Overviews, these clicks are higher quality, where users are more likely to spend more time on the site.' Implies AIO click signals loop back into NavBoost with stronger engagement metrics
- ABC Signals: Clicks as dwell time (PXR0356, HJ Kim Interview Feb 2025): Clicks (C) in ABC signals historically measured how long a user stayed at a linked page before bouncing back to SERP. NavBoost is one of the 'high-level buckets' composing the final IR score, alongside ABC-topicality and Quality
- Showing clickable results is NOT the goal (UPX0192, Lehman Jun 2016): 'Showing results that users want to click is NOT the ultimate goal' because it would promote clickbait, promote irrelevant appeal, be too forgiving of optionalization, and demote official pages. 'But showing results that users want to click is CLOSE to our goal... we can do this almost right thing extremely well by drawing upon trillions of examples'
- Engineers resented NavBoost's dominance (UPX0197, Grushetsky Jan 2019): 'engineers outside of NavBoost team used to be also not happy about the power of Navboost, and the fact it was stealing wins'
- Designing UX to learn from users (UPX0203, Lehman Dec 2016): 'In designing user experiences, SERVING the user is NOT ENOUGH. We have to design interactions that also allow us to LEARN from users.' Goal: 'sustain the illusion that we understand'
- Squeezing fractions of bits at scale (UPX0219, Lehman May 2020): 'Not just one ranking system learns from search logs. Learning from logs is the main mechanism behind ranking.' All major ML systems rely on logs: RankBrain, RankEmbed, DeepRank. 'If we can squeeze a fraction of a bit more meaning out of a session, then we get like a billion times that the very next day.' Value judgments extracted by translating logged events (clicks, swipes, views, scrolls, pauses) to guesses at what was good or bad
- Google's two-way dialogue magic (UPX0228, Google is Magical): 'This two-way dialogue is the source of Google's magic. With every query, we give some knowledge, and get a little back... After a few hundred billion rounds, we start lookin' pretty smart!' 10 blue links implicitly pose: 'Which result is best?' Titles and snippets provide background; the answer is a click
- Three Pillars of Ranking (UPX0004, Life of a Click, Eric Lehman): Body (what the document says about itself), Anchors (what the Web says about the document), User-interactions (what users say about the document). 'User-interactions include clicks, attention on a result, swipes on carousels and entering a new query'

### CRAPS Data Architecture (CrapsData Protobuf) (leak)
- patternLevel - URL abstraction hierarchy for signal inheritance. Level 0: full URL (exact page). Level 1: host pattern (p://example.com). Level 2: domain pattern (p://def.ghi). Confirms click signals propagate from pages up to domain level
- sliceTag - Flexible segmentation key allowing ad-hoc signal slicing by device, country, locale, or custom dimensions. Enables arbitrary experimental signal cuts
- mobileSignals - Separate CrapsClickSignals instance from "tier 1/2 mobile interfaces in QSessions." Not just device slicing but independent mobile click signal computation
- CrapsDevice.uxTier - Browser capability tier: 1 = modern browsers, 2 = middle tier, 3 = very old browsers. Disproportionate low-tier clicks may flag automated manipulation (bots use outdated user agents)
- CrapsDevice.os - Operating system tracked per click session, enabling OS-level signal segmentation
- packedIpAddress - Network byte order IP string in CrapsData, used by CrapsIpPrior system (references internal craps-ip-prior.h)
- unscaledIpPriorBadFraction - IP-level reputation score before linear scaling/offset/min-max transformation (references craps-penalty.cc). IP block reputation may explain shared hosting ranking disadvantages (Reboot Online experiment by Shai Aharony and Oliver Sissons)

### Domain Quality Consistency (StatsWithWeightsProto) (leak)
- StatsWithWeightsProto - Statistical distribution model with 15 attributes measuring click quality consistency across all URLs within a patternLevel (domain/host/URL). Fields: n (sample size), weightedN, mean, median, stddev, variance, varOfMean, stdError, hi, lo, pc10, pc25, pc75, pc90, kind
- Consistency over spikes: A domain with median good-click ratio 0.6 and tight standard deviation signals consistently strong quality. The same mean but massive standard deviation signals inconsistent quality across pages, likely triggering site-wide demotion
- Percentile breakpoints: pc10 (10th percentile) captures worst-performing pages. A domain's weakest pages drag down the entire profile. Best-performing pages alone are insufficient if the bottom 10% performs poorly
- patternSccStats - Links StatsWithWeightsProto to CrapsData, analyzing distribution of click quality across URLs within each pattern level

### 14 Patents (patent)
- Implicit user feedback: long clicks vs short clicks as relevance signals, modifying ranking based on view duration (US11816114)
- User behavior ranking: ranking documents using click probability models trained on user behavior and feature data (US7716225)
- Site quality scoring: dwell time and pogo-sticking measurement for site-level quality assessment (US9195944)
- Repeatable queries: identifying queries users submit repeatedly, learning repeatability models to serve cached high-quality results (US11868417)
- Location quality: inferring location quality from travel time investment, comparing anticipated vs actual distance users travel (US9558210)
- List generation: on-topic probabilistic models for automatically creating ranked lists from user behavior patterns (US7350187)
- US9684697: Ranking search results; US7769751: Method and apparatus for classifying documents based on user inputs; US7937340: Automated satisfaction measurement for web search
- US7373599: Method and system for optimum placement of advertisements on a webpage; US7401072: Named URL entry; US7149743: Virtual directory
- US6470269: Method for providing time discrimination in the world wide web; US8065296: Systems and methods for determining a quality of provided items

### Click Probability Model (exploit)
- ClickProbability - Per-result predicted CTR for every organic position
- Google builds a prediction model of expected click-through rate
- System builds per-result expected click rate, then measures over/underperformance against prediction
- Results can over/underperform against this prediction
- Click probability prediction can be modified through changes to page titles and other SERP-visible elements
- "Google doesn't use CTR in ranking" is technically true: they use a predicted CTR model
- Google Ads Planner provides hints about estimated CTR
- Candour exploit: 2TB data across 90M queries confirmed click probability exists for every organic result
- Confirmed via $13,337 Google Vulnerability Reward Program bounty to Mark Williams-Cook (Candour Agency, Dec 2024)
- Yandex cross-reference: Yandex leak confirms position-adjusted CTR expectations: pages ranking lower that receive clicks comparable to higher positions get extra credit. This validates a CTR prediction model. Yandex also tracks bookmarking and direct returns as discrete positive signals, and segments click data by geography (regional CTR variation), suggesting NavBoost likely maintains geo-segmented click profiles beyond the confirmed location slicing

### Category-Normalized Dwell Time (Patent US9514194) (exploit)
- Your dwell time is judged against your category peers, not the entire web: Patent US9514194 (Navneet Panda) reveals Google normalizes dwell time by content category. A cooking recipe site held for 5 minutes is compared to the median for other cooking sites, not all websites. A news site where users spend 2 minutes may score well because that is above the news category median. Understand what dwell time benchmarks look like in your specific content category.
- Direct URL traffic (type-in) gets a duration boost: The patent explicitly states that durations from direct URL input are boosted (e.g., 1.5x multiplier). This confirms that type-in traffic is a positive quality signal. Brand building that drives direct visits to your site improves your duration performance score, independent of search-originated visits.
- Last-visited durations are discounted to filter noise: When a user leaves your site, the duration of the last page they visited is discounted or ignored. This filters out short clicks (pogo-sticking back to SERP) and artificially long durations (user left tab open, went to lunch). Your mid-session page durations matter more than exit page durations.
- Your site gets multiple category scores, weighted by relevance: Sites belong to multiple categories with different weights. A sports news site might be 70% sports, 30% news. The Duration Performance Score is a weighted combination of how the site performs against peers in each category. Staying focused on your core categories strengthens your score.

### NavBoost (DOJ: Core click aggregation system)
Aggregates click data over 13 months into a lookup table (pre-2017: 18 months, per Mehta FOF 96). For each URL, tracks good clicks (long dwell), bad clicks (pogo-sticking), and last longest clicks (strongest satisfaction). Data sliced by location and device type. Not ML, just statistical aggregation. "Just a big table" achieving 91% accuracy improvements. Mehta opinion: 13 months of Google data equals over 17.5 years of Bing data. NavBoost beats LLMs on freshness (FOF 103).
Formula: NavBoost(url, loc, device) = lookup(
  click_table[url][loc][device]
)  // retention: 13 months rolling window
// NOT ML. Statistical aggregation only.
// accuracy improvement: 91% (Nayak testimony)
Patents: US8661029 (ranking based on click data), US8874555 (modifying search result ranking), US8244701 (rapid behavior-based index updates), US7716225 (ranking by user behavior + feature data)
Affects: Affected by: user engagement metrics. Increase dwell time, reduce pogo-sticking. Content that satisfies search intent keeps users on page.

### goodClicks / badClicks / lastLongestClicks (DOJ testimony)
goodClicks = clicks with extended dwell time on destination. badClicks = immediate returns to SERP (pogo-sticking). lastLongestClicks = final result clicked with significant dwell time (strongest signal). Raw counts per URL aggregated over 13 months.
Formula: goodClicks(url) = count(clicks where dwell_time > threshold)
badClicks(url) = count(clicks where pogo_stick == true)
lastLongestClicks(url) = count(clicks where
  is_last_click && dwell > long_threshold
)  // strongest satisfaction signal
Affects: Affected by: content quality and relevance to search intent. Engaging content = more good clicks. Misleading titles = more bad clicks.

### CRAPS (DOJ: Click and Results Prediction System)
Processes aggregated NavBoost click data into ranking adjustments. Core integration point for weighted user signals. Converts raw click counts into ranking modifiers (promotions and demotions).
Formula: CRAPS(url) = transform(
  goodClicks, badClicks, lastLongestClicks
) → ranking_modifier  // +boost or -demotion
// core integration: NavBoost → CRAPS → Ascorer
Affects: Affected by: aggregate user satisfaction patterns. Consistently good user experience improves CRAPS output.

### squashedClicks / unsquashedClicks (DOJ + Leak + Patent US8046371B2)
CrapsClickSignals stores both squashed and unsquashed versions of every click metric (clicks, impressions, lastLongestClicks). The squashing function (referenced in Patent US8046371B2 on local search scoring) applies a normalizing curve (logarithmic, sigmoid, or hard ceiling) that prevents high-traffic pages from dominating. A BBC article with 50,000 clicks does not drown out a specialist blog with 500 clicks because squashed ratios compress the scale. Architecture: unsquashed data feeds anti-spam detection (raw volumes reveal manipulation); squashed data feeds the ranking algorithm (normalized ratios determine position).
Formula: squashed(signal) = normalize(raw_signal)
// logarithmic/sigmoid/ceiling compression
// 50K clicks vs 500 clicks: ratios matter, not volumes

CrapsData.squashed = CrapsClickSignals(normalized)
CrapsData.unsquashed = CrapsClickSignals(raw)
// squashed → ranking algorithm
// unsquashed → anti-spam detection
Patent: US8046371B2 (local search scoring with squashing functions)
Affects: Affected by: natural click patterns. Artificial click inflation gets squashed. Organic diverse engagement is more resilient. Unsquashed data exposes manipulation attempts.

### serpDemotion / navDemotion (Leak: Two-stage dissatisfaction measurement)
Google measures user dissatisfaction at two distinct stages. serpDemotion: triggered by pogo-sticking on the SERP itself, penalizing pages whose title/snippet set inaccurate expectations. navDemotion: triggered by poor on-page experience after click-through (slow loading, confusing navigation, content mismatch). Both are pre-computed integer values that function as direct demotion multipliers in the ranking formula. A poor experience is a quantifiable liability.
Formula: serpDemotion(url) = aggregate(
  pogo_stick_rate on SERP
) → integer_multiplier

navDemotion(url) = aggregate(
  on_page_dissatisfaction
) → integer_multiplier
// Two-stage: promise (SERP) vs delivery (page)
// Both are pre-computed, stored in CompressedQualitySignals
Affects: Affected by: SERP snippet accuracy (serpDemotion), page load speed, navigation clarity, content-snippet alignment (navDemotion).

### voterTokenCount (User Diversity) (Leak: CrapsData protobuf)
Counts distinct voter tokens as a lower bound on distinct users contributing clicks to a query-document pair. Used for both anti-manipulation detection and privacy-related filtering. High click volume from few tokens indicates artificial manipulation. Low token counts may suppress the NavBoost signal entirely to protect user privacy. The GlueVoterTokenBitmapMessage extends this concept to all SERP features via a 256-bit bitmap capable of tracking up to 256 distinct voter token hash buckets.
Formula: voterTokenCount(query, url) =
  countDistinct(voter_tokens)
// 500 clicks from 3 tokens = red flag
// 500 clicks from 480 tokens = healthy organic
// below minimum threshold → signal suppressed (privacy)
// Glue extends via 256-bit bitmap for all SERP features
Affects: Affected by: genuine user diversity. Organic content attracting diverse real users survives this filter. Artificial click campaigns with limited user accounts are detected.

### patternLevel (Signal Inheritance Hierarchy) (Leak: CrapsData protobuf)
Integer field defining URL abstraction level for signal aggregation. Level 0 = full URL (page-specific signals). Level 1 = host pattern (subdomain-level aggregation). Level 2 = domain pattern (entire domain). Click signals computed at Level 0 propagate upward: a domain's overall click quality profile is the aggregate of all its pages' signals. This confirms domain-level signal inheritance for NavBoost.
Formula: patternLevel(0) = full_url
// https://example.com/services/seo-audit
patternLevel(1) = host_pattern
// p://example.com
patternLevel(2) = domain_pattern
// p://def.ghi
// signals propagate: page → host → domain
Affects: Affected by: site-wide click quality. Poor-performing pages drag down the domain profile. Consistent quality across all URLs strengthens domain-level signals.

### StatsWithWeightsProto (Domain Consistency) (Leak: QualityNavboost protobuf)
Statistical distribution model analyzing click quality consistency across all URLs within a pattern level. Captures sample size (n), central tendency (mean, median), dispersion (stddev, variance), and percentile breakpoints (pc10, pc25, pc75, pc90). Linked to CrapsData via patternSccStats. A domain with consistent quality across pages (tight distribution) scores better than one with the same average but high variance.
Formula: StatsWithWeightsProto(pattern) = {
  n, mean, median,
  stddev, variance,
  pc10, pc25, pc75, pc90
}
// mean=0.6, stddev=0.05 → consistently strong
// mean=0.6, stddev=0.3 → inconsistent → demotion risk
// pc10 = worst pages drag down entire domain profile
Affects: Affected by: quality consistency across all pages. Prune or improve underperforming pages. The bottom 10% (pc10) of your site's click quality hurts the whole domain.

### Rapid Behavior Index Updates (Patent US8244701 (Google, 2011))
Architectural patent describing how Google applies recent user behavior data to search rankings without waiting for full index rebuilds. The system publishes update files containing significant behavior changes to in-memory index managers on multiple index-serving machines. Each machine filters relevant updates, translates URLs to document IDs via mapping schedules (seenURL chunks), and updates the in-memory index. Dual index strategy: one index serves queries while the other is updated, then they swap. This is the infrastructure that enables NavBoost click signals to affect rankings rapidly rather than waiting for batch processing.
Formula: BehaviorUpdate(signal) = publish(
  update_file[significant_changes]
) → in_memory_index[N machines]
// each machine filters for relevant docs
// URL → docID via seenURL mapping schedule
// dual index: serve from A, update B, then swap
// enables rapid click signal propagation
Patent: US8244701 (Using behavior data to quickly improve search ranking)
Affects: Affected by: recency of user behavior data, significance threshold for updates, index serving infrastructure. Explains how NavBoost click signals propagate to rankings faster than full index rebuilds.

### CAS Model (Clicks, Attention, Satisfaction) (Google Research Paper (Chuklin & de Rijke, 2016))
Google's SERP evaluation model jointly captures click behavior, user attention, and satisfaction. Addresses challenges traditional click models miss: (1) 'good abandonments' where users find answers without clicking, (2) non-linear SERP layouts with diverse features, (3) utility gained directly on the SERP without click-through. Each SERP item generates a feature vector determining examination probability, click probability, and utility contribution.
Formula: CAS(serp_item) =
  P_examine(features) ×
  P_click(features | examined) ×
  utility(examined, clicked)
  → satisfaction_score
// Accounts for good abandonments (no click != bad)
// Handles non-linear SERP layouts (panels, carousels)
// Better agreement with user-reported satisfaction
Research: CAS Paper (CIKM 2016)
Affects: Affected by: SERP presentation quality, how well content satisfies without requiring click-through, user attention patterns.

### Duration Performance Score (Category-Normalized Dwell Time) (Patent US9514194 (Navneet Panda, James A. Kunz, 2016))
Google does not evaluate dwell time as a single raw number. This patent reveals that dwell time is normalized by content category: a cooking recipe page held for 5 minutes is evaluated against the median for cooking sites, not all sites. The system: (1) measures session durations across all pages on a site, (2) assigns the site to multiple weighted categories, (3) computes a cross-website category duration score (CWCDS) as the median dwell time for all sites in that category, (4) scores each site relative to its category peers. The output is a Duration Performance Score (DPS) used in ranking, crawl prioritization, and demotion protection. Key detail: direct URL input (type-in traffic) durations are boosted by a multiplier (e.g., 1.5x), confirming that type-in traffic is a quality signal. Last-visited durations are discounted to filter noise from short clicks and idle tabs.
Formula: WDS(site) = median(session_durations[all_pages])
// Discount last-visited duration (filters short clicks)
// Boost direct URL input durations (type-in = quality signal)

CWCDS(category) = median(
  WDS[all_sites_in_category]
) // cross-website category duration score

CDS(site, cat) = compare(
  WDS(site) vs CWCDS(cat)
) × category_weight

DPS(site) = f(
  CDS_1, CDS_2 ... CDS_k
) // e.g., count_above_threshold / total_categories
// Used in: ranking, crawl priority, demotion protection
Patent: US9514194 (Website duration performance based on category durations)
Affects: Affected by: how long users stay on your site relative to category peers. A recipe site is compared to other recipe sites, not news sites. Direct/type-in traffic boosts your score. Short clicks and pogo-sticking are discounted.

### Implicit Feedback Classification (Patent)
Classifies user clicks into quality tiers based on view duration. Long clicks (extended dwell time) are positive relevance signals; short clicks (quick bounce) are negative. The system modifies search result ranking by aggregating implicit feedback signals across users for each query-document pair. Jeffrey Dean's patent extends this with a link click probability model trained on user behavior features.
Formula: implicitFeedback(click) = {
long_click: dwell > threshold_long,
short_click: dwell < threshold_short
}
rankModifier(url, query) = aggregate(
long_clicks - short_clicks, users
)  // per query-document pair across users
Patents: US11816114 (implicit user feedback ranking modification), US7716225 (ranking by user behavior + feature data)
Affects: Affected by: user dwell time, bounce rate, task completion signals. Content that genuinely satisfies search intent generates long clicks.

### Repeatable Query Identification (Patent)
Identifies queries that users submit repeatedly over time and learns a repeatability model. For repeatable queries (e.g., 'weather', 'stock price AAPL'), the system can serve cached or pre-computed high-quality results. The repeatability learning model uses temporal patterns and user session data to classify query types.
Formula: repeatability(query) = model(
temporal_pattern, user_sessions, frequency
) → repeatable_score
// high score → serve cached/pre-computed results
// identifies habitual query patterns
Patents: US11868417 (repeatable query identification and issuance)
Affects: Affected by: query frequency patterns, temporal regularity, user session repetition. Informational queries about changing data tend to be repeatable.

## Pipeline
- **User Interaction**: Clicks, dwell, scrolls, Chrome data
- **Glue Logger**: Real-time SERP interaction capture
- **NavBoost Table**: 13 months of click data, sliced by location/device
- **P* Score**: Popularity: NavBoost + anchor metrics
- **Post-Ranking**: Re-ranks Mustang results before serving

## Timeline
- 2005: Click-based Ranking Patents - Early Google patents on using click data to adjust search rankings
- 2011: Rapid Behavior Index Updates (US8244701) - Google patents in-memory index update system that applies recent user behavior data to rankings without full index rebuilds. Dual index strategy (serve from one, update the other) enables rapid click signal propagation. Infrastructure that supports NavBoost's near-real-time ranking impact.
- 2012: NavBoost System Emerges - Internal click aggregation system replaces earlier click-based approaches
- 2014: Mobile NavBoost Launched (UPX1087) - Mobile NavBoost launched Q1 2014 as separate system from desktop NavBoost. Produced 'huge LE win,' proving mobile and desktop search intents diverge. Mobile-to-desktop volume ratio already 2:3 by August 2014. Top mobile intents: local business seeking, porn (private device), event monitoring.
- 2016: Lehman: Unified Click Prediction (UPX0192) - Eric Lehman presentation (Jun 2016): 'Reliance on user feedback in ranking has steadily increased over the past decade.' ~1B new behavior examples/day, ~100B clicks in training corpus. 'Showing results users want to click is CLOSE to our goal.' Multiple overlapping click prediction systems: 'Predictionosaurus Clickus.' Evaluation: 'train on the past, predict the future.'
- 2019: Grushetsky: NavBoost Most Powerful Signal - VP Alexander Grushetsky internal email (DOJ exhibit): "NavBoost alone was likely more powerful than the rest of ranking combined." Source: justice.gov/d9/2023-10/417245.pdf
- 2019: Chrome Data Integration - Chrome browser usage data begins feeding into NavBoost popularity signals
- 2015: Site Quality Scoring (US9195944) - Patent by Vladimir Ofitserov on scoring site quality using dwell time and pogo-sticking signals. Measures visit duration and return-to-SERP timing as quality indicators at the site level, not just page level.
- 2016: CAS Model Published - Google researchers publish CAS (Clicks, Attention, Satisfaction) model for SERP evaluation. Accounts for good abandonments and non-linear layouts. Better satisfaction prediction than click-only models.
- 2016: Category-Normalized Dwell Time (US9514194) - Navneet Panda patents category-duration normalization: dwell time scored relative to content category peers. A recipe site compared to recipe sites, not news sites. Direct URL input durations boosted. Last-visited durations discounted.
- 2023: Yandex Leak Cross-Reference - Yandex source code leak reveals 102 dwell time factors (TG_USERFEAT_SEARCH_DWELL_TIME), position-adjusted CTR expectations, and bookmarking as a discrete positive signal. User signals account for 19% of all Yandex factors (the largest single category), validating Google's heavy reliance on NavBoost. Yandex's FI_QUERY_DOWNER_CLICKS_COMBO (+0.369 weight) is the second-highest positive factor in their entire system.
- 2024: API Leak: Six NavBoost Protobuf Models - 84 mentions of NavBoost in ContentWarehouseAPI. Six QualityNavboost protobuf models exposed: CrapsClickSignals (10 attributes), CrapsData (22 attributes), CrapsDevice (3), FeatureCrapsData (6), StatsWithWeightsProto (15), GlueVoterTokenBitmapMessage (2). Reveals patternLevel hierarchy, voterTokenCount, IP reputation scoring, squashed/unsquashed architecture, uxTier browser capability tiers, and domain quality consistency measurement.
- 2024: Candour: Click Probability & Query Classifications - Mark Williams-Cook (Candour Agency) discovered Google endpoint exposing 2TB data across 90M queries. Confirmed per-result click probability prediction model. Revealed 8 query semantic classes and consensus scoring. Google paid $13,337 bounty.
- 2024: serpDemotion / navDemotion Revealed - API leak exposes two-stage demotion system: serpDemotion (pogo-sticking penalty) and navDemotion (poor on-page experience). Both are pre-computed integer demotion multipliers stored in CompressedQualitySignals.
- 2024: Mehta Liability Opinion (Aug 2024) - Judge Mehta's 286-page opinion finding Google guilty of monopoly maintenance. Key NavBoost findings: Pre-2017, NavBoost trained on 18 months of data; now 13 months (FOF 96). 13 months of Google data = 17.5 years of Bing data (Whinston testimony). NavBoost beats LLMs on freshness (FOF 103). LLMs did NOT replace NavBoost/QBST (FOF 102). QBST is a 'memorization system' trained on 13 months of user data (FOF 95). 'Navboost remains one of the most power[ful] ranking components historically' (UPX190).
- 2015: Priors Algorithm: Click Popularity = Strongest Signal (UPX0205) - Research & MI All-Hands (Apr 2015): 'Score the doors based on how many people took it. This is simple, yet very powerful: it is one of the strongest signals for much of Google's search and ads ranking!' Google describes results 'based on the people who took it,' matching users by behavioral similarity, not demographics.
- 2016: Giannandrea: Click Data Is Secret Sauce (UPX0235) - During Apple ISA negotiations (Aug 2016), Apple requested click data. Alegre: 'we don't share this information as it is key to our secret sauce.' JG (Giannandrea): 'if its which web pages people ultimately visit for a query we would *not* share that ranking signal.' Confirms click destinations and dwell time are ranking signals Google guards most.
- 2024: NavBoost Queries Feed Gemini Pre-Training (PXR0095) - Search GenAI planning doc: 'Append anonymized NavBoost Queries for selected documents' to Gemini v3-xs pre-training. NavBoost data now feeds both click-based ranking AND LLM quality. 'Anon navboost queries could be huge, given we see MMLU increases.' The click data moat extends to LLM quality.
- 2025: DOJ Remedies: Kim & Nayak Interviews (PXR0356, PXR0357) - HJ Kim interview (Feb 2025): Confirms he created NavBoost and page quality team (~2008). Reveals T* stabilized ~2020. Explains hand-crafting philosophy: 'if anything breaks Google knows what to fix.' Q* can be query-dependent in certain cases. Google acknowledged API leak: 'documents named components but don't go into curves/thresholds.' Nayak interview (Jan 2025): NavBoost is a 'QD table used in both directions.' Top-level signals are 'linear combination of log of individual raw signals' with monotonicity constraint. Google 're-thinking search stack from ground-up with LLM.' ML training windows shrinking (90 days, 60 days). RankEmbed 'performs poorly for tail queries.' FastSearch (RankEmbed-only) used as RAG for Gemini app.
- 2016: Eric Lehman: 'We Do Not Understand Documents' (UPX0203) - Q4 Search All Hands (Dec 2016). Lehman: 'We do not understand documents. We fake it. Today, our ability to understand documents directly is minimal. So we watch how people react to documents and memorize their responses.' And: 'In designing user experiences, SERVING the user is NOT ENOUGH. We have to design interactions that also allow us to LEARN from users. That is how we sustain the illusion that we understand.'
- 2018: Ranking for Research: Clicks Are Secret (UPX0204) - Internal document (Nov 2018): 'Do not discuss the use of clicks in search, except on a need-to-know basis with people who understand not to talk about this topic externally. Google has a public position. It is debatable. But please don't craft your own.' Lists 18 search quality aspects. Also: 'The association between observed user behavior and search result quality is tenuous.'
- 2018: Better Previews = Better Clicks = Better Ranking (UPX1115) - Search Quality All Hands 2018: 'Better result previews can lead to happier users and more informed user interactions. This improves the training data for models used in ranking and language understanding. This can in turn improve results and result previews thus creating a virtuous cycle.'
- 2020: Search/Ads Wall Confirmed (UPX0708) - Paul Haahr (Jun 2020): 'there can be pressure via Ads to change the product to favor Ads in general more -- holding back Search changes that are good for users but bad for revenue.' Nayak confirmed two principles: honest results and Search/Ads wall ('we don't use ads signals in search'). Protection depended on individuals, not institutional structures.
- 2020: Logging & Ranking: Value Judgments Foundation (UPX0219) - Lehman (May 2020): 'Value judgments are the foundation of Google search.' 'Learning from logs is the main mechanism behind ranking.' 'Ranking is an extreme outlier in business impact.' 'A slight improvement in value judgments pays off a billion-fold the very next day.' Multiple clicks on 3 results = all likely bad because hard query.

## Contradictions
- Public (Google Public): "We do not use clicks directly in ranking. Click data is too noisy to be a reliable signal."
  Internal (DOJ Trial (Nayak)): NavBoost achieves 91% accuracy improvements. 13 months of click data retained. Sliced by location and device for contextual precision.
- Public (Google Public): "Chrome browsing data is not used for ranking purposes."
  Internal (API Leak + DOJ): chromeInTotal, chrome_trans_clicks, uniqueChromeViews, chromeFormSubmissions, chromeScrollingIntensity, chromePurchaseActivity all confirmed in ranking pipeline.
- Public (Gary Illyes (Google)): "We don't use click-through rate directly in ranking."
  Internal (Candour Exploit): Per-result ClickProbability prediction model exists. Google predicts expected CTR and uses over/underperformance. Technically not "direct" CTR, but a prediction model based on CTR.
- Public (Google Public): "Click data is just one of many signals, and it's quite noisy."
  Internal (DOJ Exhibit (Grushetsky email, 2019)): VP Alexander Grushetsky: "NavBoost alone was likely more powerful than the rest of ranking combined." Source: justice.gov/d9/2023-10/417245.pdf. Not one of many signals, but the dominant signal.
- Public (Google Public): "Click data is too noisy to be a reliable signal. We use sophisticated AI to understand content."
  Internal (DOJ Exhibit UPX0203 (Lehman, Dec 2016) + UPX0204 (Nov 2018)): Eric Lehman (Q4 All Hands): "We do not understand documents. We fake it. We hardly look at documents. We look at people." Internal policy (Ranking for Research): "Do not discuss the use of clicks in search. Google has a public position. It is debatable. But please don't craft your own."
- Public (Nayak (DOJ Trial, 2023)): "NavBoost is just one of over a hundred signals."
  Internal (DOJ Remedies PXR0357 (Nayak, Jan 2025)): Nayak confirms Google avoids simply 'predicting clicks' but learning from logs IS the main mechanism. Top-level signals are a 'linear combination of log of individual raw signals.' Google is now 're-thinking their search stack from the ground-up with LLM taking a more prominent role.'
- Public (Nayak (DOJ Remedies PXR0357, Jan 2025)): "Google avoids simply predicting clicks because clicks are easily manipulated and are a poor proxy for enhancing user experience."
  Internal (DOJ Exhibits UPX0192 + UPX0219 + UPX0197 (Lehman & Grushetsky, 2016-2020)): Lehman (UPX0192, 2016): 'Strategy: Predict what results users will click. Boost those results.' Lehman (UPX0219, 2020): 'Learning from logs is THE MAIN MECHANISM behind ranking. ALL major ML systems rely on logs.' Grushetsky (UPX0197, 2019): 'NavBoost alone was more positive on clicks than the rest of ranking combined.' Nayak's distinction is between RAW clicks (poor proxy) and PROCESSED click signals (NavBoost, goodClicks/badClicks, dwell time), which are the backbone. The processing is the innovation, not the avoidance of clicks.
