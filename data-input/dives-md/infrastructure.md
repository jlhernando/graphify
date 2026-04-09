# Infrastructure: The Search Pipeline

## Key Insight
Google indexes approximately 400 billion documents (Pandu Nayak, sworn). Crawl frequency is driven by engagement data (HJ Kim), creating a feedback loop where popular pages get crawled more, stay fresher, and rank higher. DocID profiles track per-document signals that evolve with user interactions. SegIndexer places documents into tiered storage (flash/SSD/HDD) based on importance. The search org has 5,000-10,000 employees (Elizabeth Reid).

### Signals (doj)
- Alexandria - Primary indexing engine, near-real-time since Caffeine (2010)
- TeraGoogle - Long-term document storage, petabyte-scale web archive
- SuperRoot - Query coordination orchestrating entire pipeline
- GoogleWebServer (GWS) - Final serving layer delivering assembled SERPs
- DocID - Per-document profiles: popularity, quality, spam scores (core)
- Multiverse - Structured data feeds from external sources into Knowledge Graph
- 400B Index - ~400 billion documents (Nayak, sworn)
- Crawl from Engagement - Engagement drives crawl priority (HJ Kim)
- Search Org Scale - 5,000-10,000 employees (Elizabeth Reid)

### Index Architecture (leak)
- SegIndexer - Tiered storage: flash (fast/important), SSD (medium), HDD (archive)
- Trawler - Web crawling system with hostAge, createdDate, expiredDate tracking
- HtmlrenderWebkitHeadless - JavaScript rendering via headless Chromium
- WebMirror - Canonicalization, deduplication via SimHash (shingleInfo), localizedCluster, duplicateClusterId
- sitechunks - Content chunking for distributed quality scoring
- numTokens / bodySize - Token count with max cap, raw body size in bytes
- language - Document language classification
- Cookbook - Runtime signal generation during query processing

### Related Patents (patent)
- GFS + MapReduce - Distributed file system and processing (2003)
- BigTable - Structured storage for document metadata (2004)
- Caffeine - Near-real-time indexing replacing batch processing (2010)
- Mobile-First - Infrastructure for mobile-primary indexing (2017)
- TPU Infrastructure - Custom hardware for ML model inference
- MAGIT/Gemini - AI Overview generation infrastructure (2025)

### How Infrastructure Affects Your SEO (exploit)
- Earn your way into Flash tier: SegIndexer places documents into Flash (fastest), SSD, or HDD storage based on NSR and NavBoost engagement. Pages in HDD tier are rarely served for competitive queries. Build consistent engagement signals and site quality to get promoted to faster tiers where your pages are retrieved first.
- Break the crawl-engagement feedback loop: HJ Kim testified that crawl frequency is driven by engagement data. Low-engagement pages get crawled less, stay stale, and rank lower. Drive initial traffic through social, email, or paid channels to generate the engagement signals Trawler uses for crawl prioritization.
- Optimize for the rendering pipeline: HtmlrenderWebkitHeadless uses headless Chromium with finite resources. Heavy JavaScript that delays rendering means your content may be indexed incompletely or late. Keep critical content in initial HTML, use progressive enhancement, and ensure JS-dependent content renders within Chromium's timeout window.
- Build your DocID profile deliberately: Every indexed page accumulates hundreds of signals over its lifetime via PerDocData, including quality scores, popularity metrics, and spam indicators. New URLs start with blank profiles. Preserve URL equity on established pages rather than creating new URLs unnecessarily, since the DocID profile takes months to mature.
- Survive the hostAge sandbox: Trawler tracks hostAge with createdDate and expiredDate. New domains face 3-6 months in HDD tier with minimal crawl attention. Acquire aged domains when possible, register domains well before launch, and avoid expired-then-re-registered domains which carry reset or negative signals.
- Signal significant updates correctly: The infrastructure distinguishes between trivial changes and significantUpdate events. Changing boilerplate or timestamps does not trigger re-scoring. Modify substantive content, update structured data, and ensure your sitemap lastmod dates reflect genuine content changes to earn re-crawling and re-scoring priority.

### Trawler (Leak)
Web crawler tracking hostAge, createdDate, and expiredDate per domain. Crawl frequency is driven by engagement data (HJ Kim testimony): popular pages get crawled more frequently, creating a feedback loop where high-engagement content stays fresher.
Formula: CrawlPriority(url) = f(
  NavBoost_engagement, NSR, changeRate
)  // higher engagement = more frequent crawl
if isHotdoc: boost crawl_frequency
// hostAge tracks domain birth, expiry dates
Patents: US7580929 (info retrieval at scale), US8180754 (document scoring)
Affects: Affected by: NavBoost engagement signals, domain age, content change frequency, server response times

### SegIndexer (Leak)
Tiered document storage system. Flash tier for important, fast-access documents. SSD tier for medium-priority. HDD tier for archive/low-priority. Tier placement determined by combining NSR (site quality) and NavBoost engagement signals.
Formula: SegIndexer(doc) = tier_assign(
  NSR(site), NavBoost_clicks, query_freq
)  // Flash > SSD > HDD by importance
if NSR > high && clicks > min: promote Flash
// tier determines retrieval latency
Patents: US7580929 (info retrieval at scale)
Affects: Affected by: NSR score, NavBoost click data, query frequency for document, domain authority

### DocID (DOJ)
Per-document profile that accumulates hundreds of signals over its lifetime. Stores quality scores, popularity metrics, spam indicators, and metadata for each indexed page. Signals evolve with user interactions over time.
Formula: DocID(page) = {
  Q*, NSR, NavBoost, SpamBrain,
  pagerank_NS, hostAge, language, ...
}  // hundreds of signals per document
// accumulates over document lifetime via PerDocData
Patents: US8180754 (document scoring)
Affects: Affected by: all ranking signals, user engagement history, content updates, spam evaluations

### HtmlrenderWebkitHeadless (Leak)
JavaScript rendering pipeline using headless Chromium browser. Pages requiring JS execution are rendered server-side to extract content that only appears after JavaScript runs. Rendered output is then indexed like static HTML.
Formula: Render(url) = HeadlessChromium(
  html, js_resources
) → rendered_DOM
if requires_JS: queue render_pipeline
// output indexed as static HTML
Affects: Affected by: JS complexity, render time, page load performance, resource availability

## Pipeline
- **Trawler**: Web crawl. Frequency = f(engagement). hostAge tracks domain age.
- **Render**: HtmlrenderWebkitHeadless: JS rendering via headless Chromium
- **WebMirror**: Canonicalization, SimHash dedup (shingleInfo), localizedCluster
- **Alexandria**: Primary index. Near-real-time since Caffeine. 400B+ docs.
- **SegIndexer**: Tier placement: Flash / SSD / HDD by importance

## Timeline
- 1998: BackRub/Google - Original PageRank and web crawler. Single-machine indexing of ~24M pages.
- 2003: GFS & MapReduce - Google File System and MapReduce enable distributed indexing at web scale.
- 2004: BigTable - Distributed storage system for structured data. Foundation for document storage.
- 2008: Chrome Launch - Chrome browser provides behavioral data at scale. Eventually captures 65%+ market share.
- 2010: Caffeine Launch - New indexing infrastructure provides near-real-time indexing. Alexandria replaces batch indexing.
- 2013: Hummingbird - Query understanding overhaul. Semantic search requires new processing infrastructure.
- 2015: TeraGoogle - Long-term web archive system scaled to petabytes. Full document storage for historical analysis.
- 2015: RankBrain Deployed - First ML model in ranking. Requires GPU/TPU infrastructure for real-time inference.
- 2017: Mobile-First Index - Infrastructure shift to crawl and index mobile versions as primary documents.
- 2019: BERT Integration - BERT model deployed for query understanding. Requires massive inference infrastructure.
- 2020: SuperRoot - Query coordination system manages the full search pipeline from query to SERP delivery.
- 2024: DOJ: 400B Documents - Pandu Nayak confirmed ~400 billion documents indexed. 5,000-10,000 employees in search org.
- 2024: DOJ: Crawl Bias - HJ Kim: crawl frequency driven by engagement data. Popular pages crawled more frequently.
- 2024: API Leak - Content Warehouse API exposes SegIndexer tiers, DocID profiles, per-document signal storage.
- 2025: AI Overviews - MAGIT/Gemini integration requires new serving infrastructure for LLM-generated results.

## Contradictions
- Public (Google Public): "All pages are treated equally in our crawling and indexing."
  Internal (DOJ Trial): CrawlFrequencyFromEngagement: HJ Kim testified that crawl frequency is driven by engagement data. Popular pages crawled more, stay fresher, rank higher. SegIndexer places documents in different storage tiers based on importance.
- Public (Google Public): "Our index comprehensively covers the web."
  Internal (DOJ Trial): 400B documents indexed, but the web has trillions of pages. Engagement-based crawl budget means vast portions of the web receive minimal crawling. HDD tier documents are rarely served.
- Public (Google Public): "Competitors can build their own search engines."
  Internal (DOJ Trial): 5,000-10,000 employees. Alexandria, TeraGoogle, SuperRoot, GWS = decades of infrastructure. $26.3B/year in default deals. The scale barrier is the real moat.
- Public (Google Public): "We don't use Chrome data for ranking." (historically implied)
  Internal (DOJ + Leak): chrome_trans_clicks, uniqueChromeViews, chromeFormSubmissions, chromeScrollingIntensity, chromePurchaseActivity all confirmed as ranking signals. Chrome IS a ranking data pipeline.
- Public (Google Public): "New sites are not sandboxed."
  Internal (Evidence): hostAge in Trawler signals, host-age-sandbox in DOJ. New domains face 3-6 month observation period in HDD tier before earning promotion to higher tiers and full crawl attention.
