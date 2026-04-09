# P*: The Popularity Score (Engagement Signal)

## Key Insight
P* is the signal that makes Google's ranking self-reinforcing. Pandu Nayak confirmed P* combines NavBoost user interaction data with link-based anchor text metrics, powered by Chrome visit data. The DOJ found this creates a feedback loop: more users means more click data, better P* scores, better rankings, more users. Judge Mehta ruled this cycle is the mechanism of monopoly maintenance. The API leak revealed specific Chrome signals: chromeInTotal (site-level engagement), chrome_trans_clicks, and uniqueChromeViews. NavBoost data retention was reduced from 18 months to 13 months pre-2017.

### DOJ Trial: P* Evidence (doj)
- P*: Dynamic real-world engagement metric. One of three top-level signals (with T* and Q*)
- Pandu Nayak: P* combines NavBoost user interaction data with link-based anchor text metrics
- Chrome visit data: Chrome browser usage directly powers P*. Google fought to keep this sealed
- NavBoost 13-month window: Rolling 13 months of click data (was 18 months pre-2017)
- Feedback loop: More users > more data > better P* > better rankings > more users. Judge Mehta identified this as monopoly mechanism
- Bing comparison: Bing would need 17.5 years of logs to match Google's 13 months of NavBoost data
- Chrome-based popularity (BR) signal: HJ Kim confirmed BR as one of the 'other signals' alongside ABC and NavBoost feeding into P*. PageRank is 'a single signal relating to distance from a known good source,' used as input to Quality score (DOJ Remedies Exhibit PXR0356 - HJ Kim Interview, Feb 2025)

### API Leak: Chrome and Engagement Signals (leak)
- chromeInTotal (QualityNsrNsrData): Site-level Chrome browser views and interactions
- chrome_trans_clicks: Chrome transition click data. Direct measurement of user navigation patterns
- uniqueChromeViews: Unique Chrome browser views per page, deduplicating repeat visitors
- chromeFormSubmissions: Form submission tracking in Chrome (engagement depth signal)
- chromeScrollingIntensity: Scroll depth and scroll behavior (content engagement proxy)
- chromePurchaseActivity: E-commerce purchase tracking via Chrome (commercial engagement)
- RealTimeBoost (Twiddler): Re-ranking twiddler incorporating real-time Chrome visit signals

### How P* Affects Rankings (exploit)
- Click quality over quantity: NavBoost measures good clicks (long dwell) vs. bad clicks (pogo-sticking). Quality matters more
- Chrome usage matters: Sites visited through Chrome contribute to P*. Chrome market share directly benefits Google's data
- Engagement depth counted: Scroll intensity, form submissions, and purchase activity all contribute to engagement signals
- Real-time boost available: RealTimeBoost twiddler can quickly elevate content based on current engagement spikes
- Self-reinforcing: Popular pages get better P*, rank higher, get more traffic, better P*. Hard for new pages to break in
- Query-independent but context-sensitive: P* is mostly stable per page but NavBoost click ratios are query-specific
- Yandex cross-reference: click/traffic signals dominate all other factors: The three highest-weighted positive factors in Yandex's entire 17,854-factor system are all click/traffic-based (combined weight: +1.278). This is the strongest external validation that P* (click + Chrome + links) is likely Google's most powerful ranking signal. Yandex also explicitly rewards traffic source diversity: sites with balanced direct + social + referral + organic traffic outrank pure-SEO sites, suggesting brand building has concrete P* impact beyond directFrac alone

### 1 Patents (patent)
- P* is mostly query-independent: a page's popularity doesn't change much per query (unlike T*)
- Combined with T* and Q* by Ascorer for initial ranking in Mustang
- NavBoost contribution to P* uses good click ratio: good clicks / (good clicks + bad clicks)
- Chrome signals computed at both page level and site level
- P* interacts with Twiddlers for post-ranking adjustments (site diversity, freshness boosts)
- User behavior ranking model - Patent US7716225 (Dean, Anderson, Battle, filed 2004): Modified PageRank using user behavior to weight links. r(A) = (a/N) + (1-a) * sum(wi * r(Bi) / |Bi|), where wi derived from link selection probability. Features: font size, position, color, anchor text, URL path length. Generates both general rules (larger font links more likely clicked) and document-specific rules. Foundation for Reasonable Surfer model

### P* (Popularity Star) (DOJ)
P* combines three categories of engagement data: NavBoost click signals (good vs. bad clicks from the 13-month rolling window), Chrome browser visit data (chromeInTotal, uniqueChromeViews, engagement depth metrics), and link-based anchor metrics (how widely linked and mentioned a page is). These produce a per-page popularity score that is mostly query-independent.
Formula: P*(doc) = combine(
  NavBoost(
    good_clicks,
    bad_clicks,
    13_month_window
  ),
  Chrome(
    chromeInTotal,
    uniqueViews,
    scrollIntensity
  ),
  LinkMetrics(anchor_diversity)
)
// Mostly query-independent per-page score
Affects: Affected by: NavBoost click quality, Chrome visit volume, engagement depth, link diversity, anchor text breadth

### NavBoost Good Click Ratio (DOJ+Leak)
NavBoost tracks all search result clicks and classifies them as 'good' (long dwell time, engagement) or 'bad' (quick return to results, pogo-sticking). The ratio of good clicks to total clicks for a query-document pair directly feeds into P*. This ratio is computed from 13 months of rolling click data across all Google users.
Formula: GoodClickRatio(query, doc) =
  count(good_clicks) /
  (count(good_clicks) +
   count(bad_clicks))
// 13-month rolling window (was 18 months pre-2017)
// Good click = long dwell, engagement
// Bad click = pogo-stick, quick return
Affects: Affected by: user dwell time, bounce rate, engagement quality, search result position

### Chrome Engagement Score (Leak)
Chrome browser data provides direct measurement of user engagement beyond search clicks. Signals include total Chrome visits (chromeInTotal), unique views, scroll depth (chromeScrollingIntensity), form submissions, and purchase activity. These aggregate into a Chrome engagement score that feeds P* at both page and site level.
Formula: ChromeEngagement(doc) =
  f(
    chromeInTotal,
    uniqueChromeViews,
    chromeScrollingIntensity,
    chromeFormSubmissions,
    chromePurchaseActivity
  )
// Both page-level and site-level scores
// Google fought to keep Chrome data sealed at trial
Affects: Affected by: Chrome market share, direct visits, user engagement depth, commercial activity

### Behavior-Weighted PageRank (Patent US7716225 (Dean, Anderson, Battle, 2004))
Modified PageRank that weights links by user selection probability rather than treating all links equally. Analyzes user behavior data to identify which links users actually click (positive instances) vs ignore (negative instances). Builds probabilistic model using feature vectors per link: font size, position, color, anchor text characteristics, URL path length, source/target document features. Generates general rules (e.g., larger font links more likely selected) and document-specific rules. Applies computed weights to PageRank formula. Foundation for Reasonable Surfer model.
Formula: r(A) = (a / N) +
  (1 - a) × sum(
    wi × r(Bi) / |Bi|
  )
// wi = user selection probability for link
// a = constant (~0.1)

wi = P(selected | features)
// features: font size, position, color,
// anchor text, URL path, domain similarity
// Foundation for Reasonable Surfer model
Patent: US7716225 (Ranking documents based on user behavior, 2004)
Affects: Affected by: link placement prominence (font size, position), anchor text descriptiveness, user click probability, link feature vectors. Prominent, well-anchored links pass more PageRank.

## Pipeline
- **Click Collection**: NavBoost collects all search result clicks. Classified as good (engagement) or bad (pogo-stick)
- **Chrome Data Aggregation**: Chrome browser visit data aggregated: views, scroll depth, form submissions, purchases
- **Link Metrics**: Anchor text breadth and link diversity metrics computed (IndyRank, anchor diversity)
- **P* Computation**: NavBoost + Chrome + link signals combined into per-page popularity score
- **Score Combination**: P* combined with T* and Q* by Ascorer. Higher P* boosts ranking within topically relevant results
- **Real-Time Adjustments**: RealTimeBoost twiddler adjusts P* for trending content and engagement spikes

## Timeline
- 2004: Behavior-Weighted PageRank (US7716225) - Dean, Anderson, Battle patent user behavior-weighted PageRank. Link weights derived from click probability using feature vectors (font size, position, color, anchor text). Foundation for Reasonable Surfer model: not all links pass equal PageRank.
- 2005: NavBoost Origins - Google begins using click data as a ranking signal. Initial click-through rate experiments. NavBoost system emerges.
- 2008: Chrome Launches - Google Chrome browser launches. Creates a direct data pipeline for user engagement measurement at scale.
- 2017: NavBoost Window Reduced - NavBoost data retention reduced from 18 months to 13 months. Still vastly exceeds any competitor's data.
- 2023: Yandex Leak Cross-Reference - Yandex's top 3 highest-weighted positive factors are all click/traffic-based: FI_URL_DOMAIN_FRACTION (+0.564), FI_QUERY_DOWNER_CLICKS_COMBO (+0.369), FI_MAX_WORD_HOST_CLICKS (+0.345). This heavily validates P* as likely the most powerful Google ranking dimension. Yandex explicitly tracks traffic source diversity (direct, organic, social, paid) as quality signals and penalizes sites relying on a single traffic channel.
- 2024: Chrome Signals Exposed - API leak reveals chromeInTotal, uniqueChromeViews, chromeScrollingIntensity, chromePurchaseActivity as P* inputs.
- 2025: P* Confirmed as Pillar - Nayak confirms P* as one of three top-level signals. Judge Mehta identifies the P* feedback loop as monopoly mechanism.
- 2025: Chrome BR Signal Confirmed (PXR0356) - HJ Kim confirms Chrome-based popularity (BR) signal as one of the 'other signals' alongside ABC and NavBoost. PageRank described as 'a single signal relating to distance from a known good source,' used as input to Quality score.
