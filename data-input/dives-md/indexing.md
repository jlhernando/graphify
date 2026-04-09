# Indexing Decisions: What Gets In (and What Doesn't)

## Key Insight
Google's index has a Soft Limit and Hard Limit (Patent US7509315). Pages receive an importance rank (0-10 scale). Once the soft limit is reached, only pages exceeding the current importance threshold get indexed. Lower-quality pages are evicted when better content arrives. The API leak revealed scaledSelectionTierRank (0-32767), which places pages into three tiers: Base (flash storage, best ranking potential), Zeppelins (SSD, mid-tier), and Landfills (HDD, lowest ceiling). A Landfills document cannot outrank a Base document regardless of other signals.

### 10 Patents (patent)
- Patent US7509315: Managing URLs - Soft/Hard limits on index size, importance rank threshold for inclusion
- Patent US8554759: Selection of Documents to Place in Search Index - Utility score predicts probability of satisfying queries
- Patent US9031929: Site Quality Score (Panda) - Site quality directly gates whether to crawl, refresh, or index
- Patent US9195944: Scoring Site Quality - User visit durations determine if pages are 'sufficiently high quality to be indexed'
- Patent US9002832: Classifying Sites as Low Quality - Link quality scores classify sites, below threshold = indexing affected
- US7599920: System and method for enabling website owners to manage crawl rate in a website indexing system; US7363279: Method and system for calculating importance of a block within a display page; US20060294086: Realtime indexing and search in large, rapidly changing document collections
- US7676759: Method and apparatus for searching data; US6278992: Search engine using indexing method for storing and retrieving data

### 5 Patents (patent)
- Patent US7725452: Scheduler for Search Engine Crawler - Three-tier crawl: Base (200M+), Daily (50M+), Real-time (5M+)
- Patent US8037054: Crawler Scheduler Using Sitemaps - Sitemap priority scores create 'must-crawl' URLs
- Patent US8666964: Managing Items in Crawl Schedule - Recrawl prioritization for important/changed pages
- Patent US8458163: Website Owner Crawl Rate Management - Site owners request faster/slower crawl via Search Console. Speed-up requests are only honored when the current rate limit is provably the binding constraint: |currentLimit - maxActualRate| < threshold. If the crawler is already below server capacity, a speed-up request won't change behavior
- Patent US7634466: Realtime Indexing - Near-real-time indexing for rapidly changing content

### API Leak: Indexing Signals (leak)
- scaledSelectionTierRank (0-32767): Determines index tier (Base/Zeppelins/Landfills), acts as ranking ceiling
- OriginalContentScore (0-512): Originality measure, low score = thin content risk for deindexing
- bodyWordsToTokensRatio: Meaningful words vs tokens, quality indicator
- onsiteProminence: Page importance within site (simulated traffic from homepage)
- homePageInfo: Trust levels: NOT_TRUSTED, PARTIALLY_TRUSTED, FULLY_TRUSTED
- QuarantineInfo: Bitmask flagging violations, indicates removal from index
- lastSignificantUpdate: Distinguishes substantial revisions from minor edits
- shingleInfo: SimHash fingerprinting for near-duplicate detection (WebMirror)

### DOJ Trial Evidence (doj)
- Elizabeth Reid: 'Index tiering and crawl scheduling are among Google's most sensitive proprietary assets'
- Google labels the 'vast majority of webpages' as 'Spam, Duplicates, and Low Quality Pages'
- Q* and P* are the two top-level signals driving crawl frequency decisions
- 'Information regarding crawl schedule reveals proprietary freshness signals and index tiering structure'
- Spam fighting depends on obscurity: 'external knowledge of spam-fighting mechanisms eliminates their value'
- Index described as 'the product of proprietary crawling, annotation, and tiering systems'

### Index/No-Index Heuristics (exploit)
- INDEXED: High importance rank + strong siteAuthority/NSR + original content + user engagement + fast server
- NOT INDEXED: Below importance threshold, pandaDemotion active, thin content, duplicate, spam flags
- Crawl budget = server response speed (faster = more budget) * popularity * update frequency
- 85% of crawl issues from structural traps (faceted nav, infinite URL params, action parameters)
- Deindexing timeline: Days 1-130 safe, Days 131-189 transitional, Days 190+ most pages dropped
- Content must be 'worth fetching' per scheduler signals; declining search demand reduces crawl budget

### Index Inclusion Decision (Patent)
Each discovered URL receives an importance rank (0-10 scale). Google maintains a Soft Limit (target index size) and Hard Limit (absolute ceiling). Once the soft limit is reached, only pages exceeding the current importance threshold are crawled and indexed. Lower-ranked pages are evicted when higher-quality content arrives. The predictedUtility component (Patent US8554759) decomposes into two probabilities: P(click|position) (position-adjusted CTR from historical data) multiplied by P(impression) (probability of appearing in results given query volume). The model is trained on features including file type, host/domain, document length, language, encoding, and link-based score.
Formula: IndexDecision(url) =
  importanceRank(url) >= threshold(histogram)
  AND siteQuality(site) > Q*_minimum
  AND NOT isDuplicate(SimHash)
  AND predictedUtility(url) > utility_cutoff

predictedUtility(url) =
  P(click | position) × P(impression)
// = expected clicks per time period
// trained on: file_type, domain, length, language, link_score
Patents: US7509315, US8554759
Affects: Affected by: importance rank, site quality (Q*), content originality, duplicate status, predicted query utility

### Crawl Priority Score (Patent)
The crawler scheduler computes a priority score for each URL determining when and how often it gets crawled. The score combines content change frequency (detected by comparing content checksums across successive downloads, stored in a history log), PageRank (link-based importance), and age (time since last fetch). The inclusion threshold is dynamically derived from histogram sampling of all known URL importance scores, with target crawl set size as an adjustable parameter. Sitemap lastmod timestamps provide an additional skip signal: if lastmod <= last_crawl_date, the URL is deferred without fetching.
Formula: CrawlPriority(url) = f(
  PageRank(url),
  changeFrequency(checksum_history),
  age(time_since_last_fetch)
)
// threshold = histogram_sample(all_url_scores, target_set_size)
if CrawlPriority >= threshold: schedule crawl
if sitemap.lastmod <= last_crawl_date: skip fetch
// Tiers: Base (200M+), Daily (50M+), Real-time (5M+)
Patents: US7725452, US8037054, US8666964
Affects: Affected by: content change rate, PageRank, crawl history, sitemap signals, server response speed

### Index Tier Placement (Leak)
After a page enters the index, it is placed into one of three storage tiers based on its scaledSelectionTierRank (0-32767). This score is language-normalized and combines site authority, user engagement (NavBoost), and content quality signals. The tier acts as a ceiling on ranking potential: Base-tier documents have the highest ceiling, while Landfills documents are severely limited regardless of other signals.
Formula: TierPlacement(doc) =
  IF scaledSelectionTierRank > T1: Base (Flash, highest ceiling)
  IF scaledSelectionTierRank > T2: Zeppelins (SSD, mid ceiling)
  ELSE: Landfills (HDD, lowest ceiling)
// Landfills doc cannot outrank Base doc
Affects: Affected by: siteAuthority, NavBoost engagement, NSR, content quality, language normalization

### Duplicate Detection (Patent)
WebMirror uses SimHash fingerprinting to detect near-duplicate content. Each document gets a 64-bit fingerprint computed from weighted features (stop words stripped, remaining terms weighted). Documents with fingerprints differing in 3 or fewer bits (k=3) are near-duplicates. A permutation-based lookup trick enables fast matching at web scale: N permutations of each fingerprint are stored in sorted tables, enabling prefix-based lookup instead of brute-force comparison against all stored fingerprints. Near-duplicates can be either discarded or kept with outgoing links suppressed (preventing link graph pollution from mirrors). One canonical URL is selected per cluster using ~40 signals via ML. Duplicate sources include: mirrors, format variants (HTML/text/mobile), prepended/appended boilerplate, site re-branding via word replacement, and content aggregators.
Formula: SimHash(doc) = 64-bit hash from weighted features
IsDuplicate(doc) =
  HammingDistance(
    SimHash(doc),
    SimHash(existing_docs)
  ) 3
// permutation-based lookup for O(1) matching at scale
// outcome: discard OR keep with links suppressed
// ~40 signals select canonical via ML
Patents: US8548972
Affects: Affected by: content similarity, SimHash fingerprint, canonical signals, content originality

## Pipeline
- **URL Discovery**: Trawler discovers URLs via crawling, sitemaps, and external feeds
- **Crawl Scheduling**: Scheduler computes priority score. URL assigned to Base/Daily/Real-time crawl tier
- **Fetch & Render**: Trawler fetches page. Rendering service executes JS if needed (Chromium/Blink)
- **Quality Gate**: Site quality (Q*), importance rank, and spam scores evaluated. Below threshold = rejected
- **Duplicate Check**: WebMirror computes SimHash. Near-duplicates clustered. One canonical selected per cluster
- **Index Admission**: predictedUtility score computed. Only top N documents admitted to Alexandria index
- **Tier Placement**: SegIndexer assigns scaledSelectionTierRank. Page placed in Base, Zeppelins, or Landfills
- **Ongoing Maintenance**: Pages re-evaluated on recrawl. Declining engagement/quality can trigger tier demotion or removal

## Timeline
- 2003: GFS & MapReduce - Google File System and MapReduce enable distributed indexing at web scale.
- 2005: Managing URLs Patent - US7509315 establishes Soft/Hard index limits and importance rank thresholds for index inclusion.
- 2008: SimHash Duplicate Detection - US8548972 introduces 64-bit fingerprinting with k=3 for near-duplicate detection across 8B pages.
- 2010: Caffeine Launch - Replaces batch indexing with continuous incremental indexing. Pages enter index in near-real-time.
- 2017: Mobile-First Indexing - Google begins indexing mobile versions of pages as primary. Desktop becomes secondary index source.
- 2024: API Leak Reveals Tiers - scaledSelectionTierRank (0-32767) exposed. Base/Zeppelins/Landfills tier system confirmed.
- 2026: Reid Affidavit - Elizabeth Reid declares index tiering and crawl scheduling as Google's 'most guarded secrets.'
