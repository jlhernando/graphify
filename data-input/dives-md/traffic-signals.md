# Traffic Signals: Source Diversity & Engagement Patterns

## Key Insight
The Yandex leak's three highest positive factors are all traffic-based: FI_URL_DOMAIN_FRACTION (+0.564), FI_QUERY_DOWNER_CLICKS_COMBO (+0.369), FI_MAX_WORD_HOST_CLICKS (+0.345). Combined weight: +1.278, dwarfing all other signal categories. Yandex explicitly tracks traffic source diversity (direct, organic, social, paid, referral) and penalizes single-channel dependency. Google's Chrome data captures equivalent patterns: directFrac, chromeInTotal, chrome_trans_clicks, and chromePurchaseActivity feed into P* (Popularity). This suggests traffic diversity is not just a brand signal but a core quality indicator.

### Google API Leak: Chrome Traffic Signals (leak)
- chromeInTotal (QualityNsrNsrData): Total Chrome browser views at site level. Direct measurement of real-world traffic volume
- directFrac (NavBoost): Fraction of direct/type-in traffic via Chrome. Higher directFrac = stronger brand authority signal
- chrome_trans_clicks: Chrome transition click data measuring user navigation patterns between sites
- uniqueChromeViews: Unique Chrome browser views per page, deduplicating repeat visitors for genuine reach measurement
- chromeFormSubmissions: Form submission tracking in Chrome as engagement depth signal
- chromeScrollingIntensity: Scroll depth and behavior measuring content engagement beyond clicks
- chromePurchaseActivity: E-commerce purchase tracking via Chrome as commercial engagement signal
- RealTimeBoost: Twiddler incorporating real-time Chrome visit signals for trending content
- socialEngagementSignal (Glue): Social media engagement measurement feeding into SERP interaction data

### DOJ Trial: Traffic as Ranking Evidence (doj)
- P*: Popularity score confirmed as one of three top-level signals (with T* and Q*). Combines NavBoost + Chrome + link metrics
- Chrome data: Google fought to keep Chrome's role in ranking sealed during the trial. Judge Mehta ruled the Chrome-NavBoost-P* feedback loop constitutes a monopoly mechanism
- directFrac: Direct/type-in traffic confirmed as feeding into site authority computation and NavBoost
- NavBoost 13-month window: Rolling 13 months of click data aggregation. Google has 9x more query data than all competitors combined
- The self-reinforcing loop: more users produce more click data, better P* scores, better rankings, more users

### DOJ Exhibits: Value Judgments & Learning Loops (doj)
- Value judgments extraction (UPX0219, Eric Lehman May 2020): 'Translate logged events (clicks, swipes, views, scrolls, pauses) to guesses at what parts of the search page were good or bad.' 'This translation is a topic of continuous, multi-year research.' 'A slight improvement in value judgments pays off a billion-fold the very next day'
- Ground truth correlation (UPX0219): 'Start with a small amount of ground truth data... Then look at all associated user behaviors.' Multi-click challenge: 'If someone clicks on three search results, which one is bad? Well, likely ALL of them, because it is probably a hard query'
- Two-way dialogue model (UPX0228, Google is Magical): Users get knowledge and give back behavioral signals with every query. 'After a few hundred billion rounds, we start lookin' pretty smart!' 10 blue links as implicit question: 'Which result is best?' with titles/snippets as background and click as answer
- Bland UI optimized for learning (UPX0228): 'This is a great UX for learning. For years, Google was mocked for great search results in a bland UI. But this bland UI made the search results great.' UI minimalism maximizes signal clarity from user choices
- Mobile traffic divergence (UPX1087, Mobile vs Desktop Aug 2014): Mobile search user behavior differs from desktop with different intents and interaction patterns. Mobile traffic exceeded desktop on weekends by Aug 2014. Mobile search 'already incorporated into many people's life cycle and the usage pattern is relatively independent of time'
- Result preview virtuous cycle (UPX1115, Search Quality All Hands 2018): 'Better result previews can lead to happier users and more informed user interactions.' Virtuous cycle: better previews -> better clicks -> better training data -> better ranking -> better previews. 'If you wondered why ranking engineers care about result previews, now you know'

### Yandex Leak: Traffic Factor Weights (exploit)
- FI_URL_DOMAIN_FRACTION (+0.564): Highest positive weight in Yandex's entire system. Rewards domain-query lexical matching. Google's queriesForWhichOfficial serves a similar function via entity recognition
- FI_QUERY_DOWNER_CLICKS_COMBO (+0.369): Second-highest positive weight. CTR and click-based signal aggregation
- FI_MAX_WORD_HOST_CLICKS (+0.345): Third-highest. Domain-query word correlation via click patterns
- Direct visit share percentage: Sites with higher direct traffic rank better. Analogous to Google's directFrac
- Organic traffic percentage: Yandex tracks percent of traffic from organic search. Unnatural traffic profiles (100% organic or 0% organic) signal potential manipulation or low brand value
- Social media traffic: Social referral traffic as a positive quality signal. Validates Google's socialEngagementSignal
- Paid traffic impact: Yandex tracks whether sites run paid ads. Google denies this affects organic ranking, but Chrome sees ad traffic sources
- Bookmarked/direct visits ratio: Users returning directly (without searching) is a strong positive signal
- Verified social accounts: Yandex uses verified social profiles as trust signals, though Google has moved away from explicit authorship

### 12 Patents (patent)
- Page load time ranking: score multiplier based on load time percentile, segmented by device and location (US8645362)
- Watch time ranking: session chain of resources aggregated for video content ranking (US9098511)
- User behavior link ranking: probabilistic link selection model from feature and behavior data, user class segmentation (US8117209)
- Country biasing: preferred country promotion/demotion based on IP and interface characteristics (US7634466)
- Local inter-connectivity reranking: BackSet of intra-result-set linking documents for reranking (US7260573)
- Weighted answer terms: question phrase clustering for generating weighted answer term vectors for passage scoring (US9135625)
- Personalized anchor text: personalized PageRank for anchor text scoring from source document reputation (US8954412)
- US20090265317: Classifying search query traffic; US7093012: System and method for enhancing crawling by extracting requests for webpages in an information flow; US7689348: Intelligent redirection of vehicular traffic due to congestion and real-time performance metrics
- US20100153391: Method and System for Ranking of Keywords for Profitability; US20120078884: Presenting social search results

### How Traffic Signals Affect Your SEO (exploit)
- Build traffic source diversity as a ranking investment: Both Yandex and Google evidence shows that sites with diversified traffic (direct + social + referral + organic) rank better than pure-SEO sites. Yandex explicitly penalizes single-channel dependency. Google's Chrome data captures these patterns. Newsletter lists, social media presence, offline marketing, and partnerships that drive non-search traffic are not just marketing channels; they directly improve ranking signals.
- Direct traffic is a concrete ranking signal, not a vanity metric: Google's directFrac measures Chrome type-in traffic as a fraction of total visits. Yandex's direct visit share percentage functions identically. Brand-building activities that get users to type your domain directly into the browser have a measurable ranking impact. Memorable domains, consistent brand exposure, and repeat-visitor strategies (email, push notifications) directly feed this signal.
- Traffic volume at scale matters through chromeInTotal: Google's chromeInTotal measures total Chrome browser views at the site level. Sites with more Chrome traffic have better P* scores. This creates the feedback loop identified in the DOJ trial. For new sites, this means reaching a critical traffic mass is essential for competitive rankings. For established sites, maintaining traffic levels prevents P* decay.
- Engagement depth signals are captured beyond simple visits: Chrome tracks not just visits but scroll depth (chromeScrollingIntensity), form submissions (chromeFormSubmissions), and purchases (chromePurchaseActivity). Pages where users engage deeply score higher than pages with high bounce rates. Design content and UX to encourage deeper engagement: interactive elements, multi-step processes, and compelling below-the-fold content.
- Social engagement feeds ranking through Glue: Google's socialEngagementSignal captures social media engagement. Yandex similarly weights social traffic. Content that generates genuine social sharing and engagement creates a measurable ranking advantage. This is not about social media links (which are nofollow) but about the traffic and engagement patterns that social activity generates.
- The Yandex evidence suggests organic traffic percentage matters: Yandex tracks the percent of traffic from organic search as a quality signal. A site with 95% organic traffic may be seen as having no real audience beyond search, while a site with 40% organic + 30% direct + 20% social + 10% referral demonstrates genuine utility. Though Google has not confirmed this specific signal, Chrome data makes it trivially computable.

### P* Traffic Components (DOJ+Leak)
P* (Popularity Star) combines three traffic data sources: NavBoost click signals (13-month rolling window of good vs bad clicks, sliced by location and device), Chrome browser data (chromeInTotal, uniqueChromeViews, scrollingIntensity, formSubmissions, purchaseActivity), and link-based anchor metrics (IndyRank, anchor diversity). The traffic components are the dominant inputs.
Formula: P*_traffic(doc) = combine(
  NavBoost(good_clicks, bad_clicks),
  Chrome(
    chromeInTotal,
    directFrac,
    scrollIntensity,
    purchases
  ),
  Social(socialEngagement)
)
// Traffic components are dominant P* inputs
// Yandex top-3 factors (+1.278 combined) all traffic-based
Affects: Affected by: traffic volume, source diversity, engagement depth, brand recognition, social engagement

### Traffic Source Diversity (Hypothesis) (Yandex Leak + Chrome Data)
Yandex explicitly tracks organic traffic percentage, direct visit share, social media traffic, and paid traffic as separate signals that together indicate traffic health. Sites with balanced traffic profiles rank better than single-channel dependent sites. Google's Chrome data can compute equivalent metrics: directFrac (already confirmed), plus chrome_trans_clicks for referral patterns and socialEngagementSignal for social traffic.
Formula: TrafficDiversity(site) = balance(
  directFrac,
  organicShare,
  socialShare,
  referralShare
)
// Hypothesis from Yandex cross-reference
// Yandex penalizes single-channel dependency
// Chrome data makes this trivially computable for Google
Affects: Affected by: brand building (direct traffic), social strategy (social traffic), content partnerships (referral), SEO (organic). Multi-channel marketing directly improves this signal.

### Site Quality from Visit Duration (Patent US9195944 (Ofitserov, 2013))
Computes query-independent site quality score from user visit duration measurements. Collects dwell time data from multiple sources: click tracking (time from search click to return), Chrome browser measurements, network monitoring, and server-side data. Discards durations below minimum threshold (e.g., <5 seconds). Caps at maximum (5-30 minutes depending on resource type: video pages get longer caps). Applies resource-type weights. Calculates central tendency (mean, median, or geometric mean) from adjusted measurements. Used for ranking, crawl priority, and indexing decisions.
Formula: SiteQuality(site) = StatsMeasure(
  filter(
    durations,
    min: 5s,
    max: cap(resource_type)
  )
)
// StatsMeasure = mean, median, or geometric mean
// cap: video ~30min, images ~5min, text ~15min
// Sources: click tracking, Chrome, network, server
// Query-independent: site-level aggregation
Patent: US9195944 (Scoring site quality, 2013)
Affects: Affected by: user dwell time, content depth, page load speed, content type (video vs text), engagement quality. Sites with consistently long visits score higher.

### Page Load Time Ranking (Patent)
Computes a score multiplier based on a page's load time relative to percentile distributions, segmented by device type and user location. Faster-loading pages receive a ranking boost; slow pages are penalized. The system measures actual load times (not synthetic), segmented by network conditions and device capabilities. The multiplier applies as a post-retrieval adjustment.
Formula: loadTimeScore(page) = multiplier(
percentile(load_time, device, location)
)  // faster than peers → boost
// slower than peers → penalty
// segmented by device + location
Patents: US8645362 (using resource load times in ranking)
Affects: Affected by: server response time, page weight, render-blocking resources, CDN coverage. Performance relative to category peers matters more than absolute speed.

### Watch Time Ranking (Patent)
Aggregates watch time across session chains of resources (videos, playlists, channels) to produce a ranking signal for video content. The system tracks not just individual video watch time but the chain of resources a user consumes in a session, attributing engagement credit across the chain. A score multiplier MQ,Di adjusts video rankings based on watch time relative to peers.
Formula: watchTimeScore(video) = MQ,Di ×
sessionChain(
watch_time[v1, v2, ..., vN]
)  // credit distributed across session chain
// not just individual video duration
Patents: US9098511 (watch time based ranking)
Affects: Affected by: video engagement depth, session continuation rate, playlist completion. Videos that keep users watching (and watching related content) score higher.

## Pipeline
- **Chrome Data Collection**: Real-time capture of visits, scroll depth, form submissions, purchases from Chrome browser
- **NavBoost Aggregation**: 13-month rolling window of click data, classified as good/bad, sliced by location/device
- **Traffic Source Classification**: Chrome transition data classifies traffic as direct, organic, social, referral, or paid
- **Engagement Scoring**: Scroll intensity, form submissions, purchase activity aggregated into engagement depth score
- **P* Computation**: Traffic signals combined with link metrics into per-page P* popularity score
- **RealTimeBoost**: Twiddler adjusts scores for trending content and engagement spikes

## Timeline
- 2004: User Behavior Ranking Patent (US7716225) - Google patents behavior-weighted PageRank. Link weights derived from user selection probability using feature vectors (font size, position, anchor text). r(A) = (a/N) + (1-a) * sum(wi * r(Bi) / |Bi|). Foundation for Reasonable Surfer model.
- 2005: NavBoost Origins - Google begins using click data as a ranking signal. Click-through rate experiments begin.
- 2008: Chrome Launches - Google Chrome browser launches, creating a direct data pipeline for user engagement measurement at massive scale.
- 2012: Page Load Time Ranking (US8645362) - Patent on using resource load times as a ranking signal. Score multiplier based on load time percentile, segmented by device type and user location. Faster pages get ranking boost relative to category peers.
- 2013: Site Quality Scoring Patent (US9195944) - Google patents query-independent site quality scores based on visit duration measurements. Statistical measure (mean/median) of dwell times from Chrome, click tracking, and server data. Discards durations below threshold, caps at maximum (varies by resource type). Used for ranking and crawl priority.
- 2014: Watch Time Ranking (US9098511) - Patent on watch time based ranking for video content. Aggregates watch time across session chains of resources. Score multiplier applied based on watch time relative to peers.
- 2019: Chrome Data Integration - Chrome browser usage data begins feeding into NavBoost and P* popularity signals via chromeInTotal, directFrac.
- 2023: Yandex Leak: Traffic Dominance Revealed - Yandex source code leak shows top 3 highest-weighted positive factors are all traffic/click-based (combined +1.278). Traffic source diversity explicitly tracked. Direct, organic, social, paid traffic percentages all serve as quality signals.
- 2024: API Leak: Chrome Signals Exposed - chromeInTotal, uniqueChromeViews, chromeScrollingIntensity, chromePurchaseActivity, chromeFormSubmissions confirmed as P* inputs.
- 2014: Mobile Surpasses Desktop on Weekends (UPX1087) - Mobile search traffic exceeded desktop on weekends by Aug 2014. Mobile intents diverge significantly: local business was top mobile intent category. Mobile search 'already incorporated into many people's life cycle and the usage pattern is relatively independent of time.'
- 2018: Preview Virtuous Cycle Acknowledged (UPX1115) - Search Quality All Hands 2018: 'Better result previews can lead to happier users and more informed user interactions.' Virtuous cycle: better previews -> informed clicks -> better training data -> better ranking -> better previews. 'If you wondered why ranking engineers care about result previews, now you know.'
- 2020: Lehman: Value Judgments Framework (UPX0219) - Eric Lehman (May 2020): 'Translate logged events (clicks, swipes, views, scrolls, pauses) to guesses at what parts of the search page were good or bad.' 'A slight improvement in value judgments pays off a billion-fold the very next day.' Multi-click challenge: 'If someone clicks on three search results, which one is bad? Well, likely ALL of them.'
- 2020: Google is Magical: Two-Way Dialogue (UPX0228) - Lehman (UPX0228): Users give behavioral signals with every query. 'After a few hundred billion rounds, we start lookin' pretty smart!' 10 blue links as implicit question mechanism. 'This bland UI made the search results great' by maximizing learning signal clarity.
- 2025: DOJ Trial: P* Feedback Loop - Nayak confirms P* as one of three top-level signals. Judge Mehta identifies the Chrome-NavBoost-P* feedback loop as a monopoly mechanism.

## Contradictions
- Public (Google Public): We don't use Chrome browsing data for ranking purposes.
  Internal (API Leak + DOJ): chromeInTotal, directFrac, chrome_trans_clicks, uniqueChromeViews, chromeFormSubmissions, chromeScrollingIntensity, chromePurchaseActivity all confirmed as ranking inputs feeding into P*.
- Public (Google Public): Social signals are not a ranking factor.
  Internal (API Leak): socialEngagementSignal captured in Glue SERP interaction data. Social traffic creates engagement patterns measured by Chrome. Yandex explicitly weights social traffic as a positive signal.
- Public (Google Public): Google Ads spending does not influence organic rankings.
  Internal (Yandex Leak): Yandex explicitly tracks paid traffic (Yandex Direct) as a ranking input. While Google denies this, Chrome can observe ad-sourced traffic patterns. Traffic diversity (including paid channels) may indirectly benefit organic signals.
- Public (Google Public): We use sophisticated AI and machine learning to understand search queries and deliver the best results.
  Internal (DOJ Exhibits UPX0219, UPX0228 (Eric Lehman)): Lehman (UPX0228): 'The source of Google's magic is this two-way dialogue with users.' The 10 blue links are an implicit question: 'Which result is best?' with click as answer. Lehman (UPX0219): Value judgments are 'guesses at what parts of the search page were good or bad' extracted from clicks, swipes, scrolls, pauses. The core mechanism is behavioral pattern memorization, not AI understanding.
- Public (Google Public): Mobile and desktop search provide equivalent experiences adapted for each form factor.
  Internal (DOJ Exhibit UPX1087 - Mobile vs Desktop Ranking (Aug 2014)): Mobile search intents diverge significantly from desktop. Mobile traffic exceeded desktop on weekends by 2014. Different intents, interaction patterns, and user behavior mean mobile ranking signals are fundamentally different, not just adapted versions of desktop signals.
