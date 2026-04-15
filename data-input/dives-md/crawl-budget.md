# Crawl Budget: Google's Resource Allocation Engine

## Key Insight
Crawl budget is not a single number but a dynamic priority queue that weighs page importance, change frequency, server responsiveness, and freshness demand. Patent US8666964 reveals Google maintains a crawl schedule with due dates per URL, sorting overdue pages by importance rank, change period, and tardiness. Judge Mehta's opinion confirms that crawl scale feeds into Google's user-data advantage, which is the actual competitive moat. Website owners can influence crawl rate (US7599920) but cannot increase crawl demand, which is determined by Google's assessment of page value.

### Crawl & Indexing Signals (leak)
- CrawlFrequencyFromEngagement (module: Infrastructure) - How often Googlebot revisits a URL, driven by engagement data. Pages with higher engagement get crawled more frequently, feeding fresher signals into ranking
- crawlPagerank (module: PerDocData) - PageRank value used during crawl scheduling. Directly gates crawl priority: higher crawlPagerank means more frequent fetching
- changeRate (module: CrawlerChangerateUrlVersion) - Content change frequency measured over crawl intervals. URLs with high change rates get recrawled more often to maintain index freshness
- contentStability (module: CrawlerChangerateUrlVersion) - Content stability metric over time. Stable pages may receive lower recrawl priority; volatile pages get higher priority
- TeraGoogle - Long-term document archive storing historical versions. Crawled content persists even after removal from the live index, enabling temporal analysis and change-rate computation

### Crawl Rate & Schedule Patents (patent)
- US7599920 - Website owner crawl rate management. Allows site owners to specify preferred crawl rates via a configuration interface. System adjusts Googlebot's fetch frequency per host while respecting server capacity thresholds
- US8458163 - Crawl rate management (continuation). Extends US7599920 with finer-grained controls: time-of-day scheduling, bandwidth caps, and priority URL patterns. Enables website owners to protect server resources during peak traffic
- US8666964 - Managing items in crawl schedule. Originally designed for Google Search Appliance (enterprise crawl). Assigns each URL a due date based on importance rank and predicted change period. Overdue pages are sorted by tardiness and importance to determine fetch order. Applicable principles extend to web-scale crawl scheduling

### Crawl Budget Optimization Tactics (exploit)
- Server response time optimization - Faster TTFB means Googlebot can fetch more pages per session. Sites responding under 200ms get significantly more pages crawled per day than those at 2s+
- Eliminate soft 404s and redirect chains - Every wasted fetch on broken URLs, soft 404s, or long redirect chains consumes crawl budget without adding value to the index
- Strategic robots.txt - Block low-value paths (faceted navigation, internal search, session URLs) to concentrate crawl budget on high-value content pages
- XML sitemap freshness signals - Accurate lastmod dates in sitemaps signal which pages actually changed. Google penalizes sites that set lastmod to current date on every page
- URL parameter handling - Consolidate parameter variations to prevent crawl budget dilution across thousands of functionally identical URLs
- Internal linking architecture - Pages with strong internal link signals receive higher crawl priority. Orphan pages deep in the site structure get crawled less frequently or not at all

### Crawl Scale and the User-Data Advantage (doj)
- Crawl feeds user-data advantage - Judge Mehta's opinion identifies user data scale as the key barrier to competition. Crawl infrastructure feeds into this advantage: broader crawl coverage produces better results, which attracts more users, generating more engagement data that further improves ranking
- CrawlFrequencyFromEngagement - HJ Kim testified (PXR0356) that crawl frequency is driven by engagement data. Popular pages get crawled more often, stay fresher in the index, and rank higher, creating a reinforcing loop
- Freshness depends on recrawl frequency - DOJ evidence shows Google's ability to recrawl at scale gives it a freshness advantage. Breaking news ranking depends on crawl speed, and competitors with smaller crawl infrastructure fall behind on time-sensitive queries
- Crawl data is the foundation of ranking - Crawled content is the raw input for every ranking system: body text for relevance, links for authority, change patterns for freshness, structured data for rich results. Without crawl, there is no ranking

### Calculations
- Effective Crawl Budget: Crawl budget is the product of the crawl rate limit (maximum fetches the server can handle without degradation) and crawl demand (Google's desire to fetch based on page value, change frequency, and popularity). Server response time acts as a multiplier on rate limit. Affects: Indexing coverage, freshness, ranking eligibility
- Crawl Priority Score: Each URL in the crawl schedule has a due date based on its importance rank and predicted change period. When a URL becomes overdue, it is sorted by tardiness (how far past due) and importance rank. The scheduler fetches the most overdue, most important URLs first. Affects: Crawl freshness, index completeness, freshness ranking signals
- Crawl Waste Ratio: Measures the fraction of crawl budget consumed by non-indexable fetches: soft 404s, redirect chains, blocked resources, duplicate parameter URLs. High waste ratios reduce effective crawl coverage of valuable content. Affects: Index coverage, freshness of important pages, crawl efficiency

### Crawl Budget Pipeline: From URL Discovery to Index
- URL Discovery: New URLs enter the crawl queue via sitemaps, internal links, external backlinks, and previously known URLs due for recrawl. Each URL is assigned an initial priority score.
- Robots.txt Check: Before fetching, Googlebot checks cached robots.txt rules for the host. Blocked paths are skipped. Robots.txt itself is recrawled periodically (typically every 24h).
- Crawl Rate Limiting: The scheduler enforces per-host rate limits based on server response times and owner-configured limits (US7599920). Fetches are throttled to avoid overloading servers.
- Priority Queue Scheduling: URLs compete for crawl slots based on due dates (US8666964). Overdue pages are sorted by tardiness and importance rank. High-value stale pages jump the queue.
- Fetch & Render: Googlebot fetches the URL, follows redirects, and renders JavaScript if needed. Response codes, headers, and content are recorded. Failed fetches trigger exponential backoff.
- Content Change Detection: Fetched content is compared against the stored version in TeraGoogle. Change deltas update the predicted change rate (changeRate signal) for future scheduling. Unchanged pages may get lower recrawl priority.
- Indexing Selection: Post-crawl triage determines whether the fetched content enters the serving index. Duplicate, thin, or low-quality content may be crawled but not indexed, wasting budget.

### Timeline
- 2005 (patent): Crawl Schedule Management Filed (US8666964) - Google files patent for managing items in crawl schedule with due dates, importance ranking, and tardiness-based sorting. Originally targeting Google Search Appliance.
- 2008 (patent): Crawl Rate Management Patent (US7599920) - Patent granted for enabling website owners to manage crawl rate. First formalization of the crawl rate limit concept as a configurable parameter.
- 2009 (public): Google Search Console Crawl Stats - Google Webmaster Tools (now Search Console) begins exposing crawl statistics to webmasters, including pages crawled per day and time spent downloading.
- 2014 (patent): Crawl Schedule Management Granted (US8666964) - Patent granted for managing items in crawl schedule with priority scoring based on importance, change period, and tardiness.
- 2017 (public): Google Defines Crawl Budget Publicly - Gary Illyes publishes official Google blog post defining crawl budget as the combination of crawl rate limit and crawl demand. First public acknowledgment of the two-factor model.
- 2024 (leak): API Leak Reveals Crawl-Freshness Connection - The Google API documentation leak exposes CrawlFrequencyFromEngagement, crawlPagerank, and changeRate signals, confirming that crawl budget allocation directly impacts ranking through engagement-driven freshness signals.
- 2024 (doj): Mehta Opinion: User Data Scale as Competitive Moat - Judge Mehta's antitrust opinion identifies user data scale as a barrier to competition. Crawl infrastructure feeds this advantage: broader coverage attracts more users, generating more engagement data that improves ranking.

### Contradictions
- Public (Google Search Central Documentation): Crawl budget is not something most site owners need to worry about. Google's crawling is efficient and rarely causes problems. vs Internal (API Leak (CrawlFrequencyFromEngagement, crawlPagerank, changeRate) + PXR0356 (HJ Kim)): Crawl frequency is driven by engagement data (CrawlFrequencyFromEngagement). Popular pages get crawled more often, stay fresher in the index, and rank higher. crawlPagerank directly gates crawl priority. Sites without engagement signals receive minimal crawl resources.

### Overview
Crawl budget determines which URLs Google fetches, how often, and at what rate. It combines crawl rate limits (server capacity) with crawl demand (page value). The DOJ trial revealed that crawl scale feeds Google's user-data advantage, while patents show sophisticated scheduling systems that balance freshness needs against server load. The API leak exposes crawl-frequency signals feeding directly into freshness scoring.
