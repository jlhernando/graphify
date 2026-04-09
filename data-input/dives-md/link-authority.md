# Link Authority: How Google Measures Trust

## Key Insight
Google publicly denied "domain authority" from 2016-2019 while siteAuthority and authorityPromotion existed as production ranking signals. PageRank was quietly replaced by PageRank_NS (Nearest Seed), which measures distance from trusted seed sites rather than raw link count. authorityPromotion actively boosts domains deemed authoritative, while unauthoritativeScore demotes those that aren't.

### Signals Confirmed (leak)
- pagerank_NS - Nearest Seed PageRank: distance from trusted seed sites (production variant)
- siteAuthority - Composite site-level trust and reputation score
- authorityPromotion - Active boost for high-authority domains
- nearestSeed - Distance to closest seed site in link graph
- homepagePagerankNs - Homepage-specific seed distance evaluation
- indyRank / scaledIndyRank - Content independence and editorial quality measurement
- NSR / newNSR - Normalised Site Rank: site-level quality aggregation

### Signals,  Core (doj)
- siteAuthority - Persistent domain reputation score (DOJ-confirmed)
- NSR - Successor to PageRank for site-level quality assessment
- predictedDefaultNSR - ML-predicted NSR for new/low-data sites
- authorityPromotion - Positive boost for trusted domains
- unauthoritativeScore - Demotion signal for low-authority sites
- pagerankSeedDistance - Explicit seed distance signal (HJ Kim deposition)
- chromeInTotal - Site-level Chrome visits as authority proxy

### 13 Patents (patent)
- TrustRank (US7603350): seed-based trust propagation through link graph
- Distance-based ranking (US9165040): shortest path to seed sites
- Seed site selection (US9953049): criteria for curating trusted seed set
- Anchortext indexing and ranking (US7398461, filed 2002): Weights anchor text tokens by frequency and assigns them as virtual document content. Token weight: W(term) = sum(f(phrase)/f(term) * ATC(phrase)) + ATC(term), where ATC = anchortext count. Normalized via sqrt(W/sum_all) * log10(W). Tokenizes anchor phrases into n-grams (e.g., 'best Louis Armstrong site' becomes 6 tokens). High-weight tokens indexed as if they appeared in the target document's important sections. Foundation for A* (Anchors) sub-signal
- Onsite/offsite combined ranking (US8843477, filed 2011): Combines global ranking (backlinks, external authority) with onsite ranking (internal site structure, internal link importance). Computes separate global and onsite scores, then generates combined rankings. Prevents site-level manipulation by analyzing internal page importance independently of external signals
- Original PageRank: node ranking in linked database, the foundational citation-weighted importance algorithm (US6285999, US7058628)
- Document inception date scoring: freshness and trust signals derived from document age, link acquisition patterns, and content change history (US7840572)
- Site-level authority aggregation via sitechunks
- Link equity decay over hop distance
- Homepage authority as proxy for domain trust
- US6601075: System and method of ranking and retrieving documents based on authority scores of schemas and do...; US7844610: Delegated authority evaluation system; US20050131722: Delegated authority evaluation system
- US20100082637: Web Page and Web Site Importance Estimation Using Aggregate Browsing History; US20100153388: Methods and apparatus for result diversification

### How Link Authority Affects Your SEO (exploit)
- Minimize hop distance from seed sites: PageRank_NS measures shortest path from your site to manually curated seed sites (confirmed by HJ Kim deposition). Getting a link from a site that is 1-2 hops from a seed site (major news outlets, .gov, universities) is exponentially more valuable than dozens of links from sites that are 5+ hops away. Map your link graph distance and target intermediate authority sites.
- Invest in your homepage's seed distance: homepagePagerankNs evaluates your homepage specifically for seed proximity. Your homepage acts as the authority proxy for your entire domain. Earn authoritative links pointing directly to your homepage, not just deep pages, because homepage authority cascades to all pages via internal linking.
- Build brand search volume for authorityPromotion: authorityPromotion actively boosts domains deemed authoritative, while unauthoritativeScore demotes those that are not. Brand recognition (measured via Chrome's directFrac and navigational queries) feeds into this binary promotion system. PR campaigns, brand mentions, and direct traffic generation are link-building alternatives that trigger the same authority signals.
- Earn chromeInTotal through direct navigation: Chrome's 65%+ browser market share provides Google with site-level visit data. chromeInTotal aggregates total Chrome visits at the domain level as an authority proxy. Encourage users to bookmark your site, type your URL directly, and return frequently, since this direct traffic signal feeds into siteAuthority computation.
- Score high on indyRank for editorial independence: indyRank and scaledIndyRank measure whether your content demonstrates editorial independence. Sites that primarily aggregate, curate, or rewrite others' content score lower. Create original research, proprietary data, unique expert perspectives, and first-party reporting that cannot be found elsewhere to maximize this signal.
- Leverage sitechunk-level authority aggregation: Authority is computed at site level via sitechunks (URL pattern groups), not individual pages. A strong section of your site lifts other sections. Concentrate your best content and link acquisition efforts on a core URL pattern (e.g., /blog/ or /guides/) to build sitechunk authority that benefits adjacent content.

### PageRank_NS (DOJ+Leak)
Nearest Seed PageRank. Measures link graph distance from topic-specific trusted seed pages, NOT raw link counting. Trust propagates through the link graph, decaying with each hop from seed sites. HJ Kim deposition confirmed this replaced classic PageRank.
Formula: PageRank_NS(url) = min_path(
url, seed_set[topic]
) * decay_per_hop
// NOT raw link counting (HJ Kim deposition)
// trust decays with each hop from seeds
Patents: US9165040 (link distances), US7603350 (trust), US9400849 (scalable short paths)
Affects: Affected by: hop distance from seed sites, link graph topology, seed site selection, link decay per hop

### siteAuthority (DOJ+Leak)
Persistent composite site-level trust score. Aggregates PageRankNS, NSR, and other quality signals into a domain-wide authority metric. Computed at site level via sitechunks. Existed as production signal while Google publicly denied "domain authority."
Formula: siteAuthority(domain) = aggregate(
PageRank_NS, NSR, Q*, engagement
)  // domain-level via sitechunks
// existed while Google denied "domain authority"
Patents: US9165040 (link distances), US7603350 (trust)
Affects: Affected by: PageRank_NS, NSR score, Q* quality, engagement metrics, domain history

### authorityPromotion (Leak+DOJ)
Active ranking boost applied to domains deemed authoritative. A positive signal that elevates trusted sites in rankings. Works alongside unauthoritativeScore (demotion) as a two-sided authority modifier.
Formula: authorityModifier(domain) =
if siteAuthority > high_threshold: +authorityPromotion
if siteAuthority < low_threshold: -unauthoritativeScore
// two-sided: boost trusted, demote untrusted
Patents: US9165040 (link distances), US9400849 (scalable short paths)
Affects: Affected by: siteAuthority threshold, domain trust level, content quality consistency, engagement patterns

### AnchorTextWeight (Patent US7398461 (Google, filed 2002))
Weights anchor text tokens by their frequency across linking documents and indexes them as virtual content of the target document. Anchor phrases are tokenized into n-grams: 'best Louis Armstrong site' becomes individual tokens and multi-word phrases. Each token's weight combines its own anchortext count with the proportional weight of phrases containing it. Tokens exceeding a threshold are indexed as if they appeared in high-importance sections of the target page. This is the mechanical foundation for A* (Anchors), the 'what the web says about a document' sub-signal of T*.
Formula: W(term) = sum(
  f(phrase) / f(term) × ATC(phrase)
) + ATC(term)
// ATC = anchortext count across linking documents
// f(x) = frequency in index

NormalizedW = sqrt(
  W / sum(all_token_weights)
) × log10(W)
// tokens above threshold indexed as high-importance content
// foundation for A* (Anchors) sub-signal of T*
Patent: US7398461 (Method for ranking web page search results, 2002)
Affects: Affected by: anchor text diversity and descriptiveness across linking documents, phrase frequency in the broader index, anchor text relevance to target page topic. Descriptive contextual anchors weight more than generic 'click here' links.

### chromeInTotal (DOJ)
Site-level Chrome visits used as an authority proxy. Total Chrome browsing visits aggregated at the domain level. Chrome's 65%+ browser market share provides broad behavioral data that feeds into authority assessment.
Formula: chromeInTotal(domain) = sum(
chrome_visits[domain], rolling_window
)  // 65%+ browser market share = broad signal
// aggregated at domain level for authority proxy
Affects: Affected by: total Chrome user visits, direct navigation frequency, Chrome browser market share, user engagement depth

### Document Inception Date Scoring (Patent)
Scores documents based on their inception date and temporal patterns. Tracks document creation date, content update frequency, link acquisition velocity, and historical change patterns. The system differentiates between genuinely old authoritative content and stale content by analyzing the pattern of changes over time. Co-invented by Matt Cutts, Jeffrey Dean, and Paul Haahr. Used for freshness scoring, trust assessment, and domain age signals.
Formula: inceptionScore(doc) = f(
inception_date,
link_acquisition_velocity,
content_change_pattern,
update_frequency
)  // old + updated = authoritative
// old + stale = low freshness score
// new + rapid links = suspicious velocity
Patents: US7840572 (document scoring based on inception date, Cutts/Dean/Haahr)
Affects: Affected by: document age, update consistency, link growth patterns over time. Established domains with consistent update histories benefit from trust signals.

## Pipeline
- **Seed Sites**: Manually curated: reliable, diverse, well-connected domains
- **Trust Propagation**: PageRank_NS: trust flows through links, decaying per hop
- **Site Aggregation**: NSR, siteAuthority: per-domain scores via sitechunks
- **Authority Modifier**: authorityPromotion boosts, unauthoritativeScore demotes
- **Q* Integration**: Authority feeds into Q* quality score for final ranking

## Timeline
- 1998: PageRank Patents (US6285999, US7058628) - Lawrence Page files two foundational PageRank patents at Stanford: US6285999 (node ranking in linked database) and US7058628 (citation-weighted importance variant). The algorithm that created Google: ranking pages by steady-state probability of a random surfer following links.
- 2002: Anchortext Indexing Patent (US7398461) - Google patents anchor text weighting algorithm. Tokens from linking pages weighted by frequency: W(term)=sum(f(phrase)/f(term)*ATC(phrase))+ATC(term). High-weight tokens indexed as virtual content of target page. Foundation for A* (Anchors) sub-signal. Tokenization breaks phrases into n-grams for comprehensive matching.
- 2004: TrustRank (US7603350) - Stanford/Yahoo research on seed-based trust. Manually curated seed sites propagate trust through link graph. Google adopts the concept.
- 2012: Distance-Based Ranking - Google patents US9165040, US9953049: rank by shortest path distance from trusted seed sites rather than raw link count.
- 2016: Google Denies Domain Authority - Google publicly states "domain authority is not a ranking factor." Internally, siteAuthority and Q* exist as production signals.
- 2024: API Leak: 14 Authority Signals - pagerank_NS (Nearest Seed), siteAuthority, authorityPromotion, indyRank, homepagePagerankNs, nearestSeed all confirmed.
- 2024: DOJ: Seed Distance Confirmed - HJ Kim deposition: PageRank measures distance from seed sites, not raw links. siteAuthority is a computed feature alongside Q*.

## Contradictions
- Public (Google Public): "There is no such thing as domain authority. We don't have a domain authority score." (2016-2019)
  Internal (API Leak + DOJ): siteAuthority is a persistent composite site-level trust score. authorityPromotion actively boosts trusted domains. Both existed while Google denied it publicly.
- Public (Google Public): "PageRank is just one of hundreds of signals and not particularly important anymore."
  Internal (API Leak): PageRank_NS (Nearest Seed) is the production PageRank variant. It replaced classic PageRank but the link-distance-from-seeds model is MORE important, not less. 14 authority signals confirmed.
- Public (Google Public): "We evaluate pages individually, not at the domain level."
  Internal (DOJ Trial): NSR aggregates authority at domain level via sitechunks. Q* quality score operates at site level. New pages inherit domain authority via predictedDefaultNSR before earning their own signals.
