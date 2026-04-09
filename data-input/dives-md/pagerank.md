# PageRank: Link Authority Mechanics

## Key Insight
The 2024 API leak revealed seven PageRank variants still active internally: pagerank, pagerank_ns, homepagePagerankNs, ToolbarPageRank, rawPagerank, pagerank2, and firstCoveragePagerank. The production version is PageRank_NS (Nearest Seed), which measures distance from trusted seed sites, not just link counting. HJ Kim confirmed PageRank feeds into Q* (Quality) as one of 'over 100 raw signals.' Pandu Nayak clarified: 'Most of Google's quality signal is derived from the webpage itself,' placing PageRank as important but not dominant. The original patent expired September 2019.

### 4 Patents (patent)
- Patent US6285999: Original PageRank (Larry Page, Stanford) - Random surfer model, damping factor 0.85, iterative eigenvector computation
- Patent US7716225: Reasonable Surfer (Dean, Anderson, Battle) - Links weighted by click probability. Font size, position, color, context all affect weight
- Patent US9165040: Seed-based distance ranking (Hajaj) - Shortest distances from diversified trusted seed pages, not raw link flow
- Patent US8117209: Reasonable Surfer update - Enhanced user behavior and feature data for link weighting
- Original patent expired Sept 2019. Assigned to Stanford (not Google). Stanford received 1.8M Google shares for exclusive license

### API Leak: 7 PageRank Variants (leak)
- pagerank: Base/classic PageRank score
- pagerank_ns: PageRank-NearestSeeds (production version). Distance from trusted seed sites
- homepagePagerankNs: Homepage-specific PageRank_NS. Trust anchor for all pages on domain
- ToolbarPageRank: Legacy metric, no longer public since 2016, still computed internally
- rawPagerank: Basic unmodified PageRank from pure link structure
- pagerank2: Updated version (exact differences unknown)
- firstCoveragePagerank: PageRank captured when Google first discovers a page
- IndyRank: Backlink independence/diversity. Many unrelated sources > cluster of related sites

### DOJ Trial Evidence (doj)
- Pandu Nayak: PageRank is 'a single signal relating to distance from a known good source, used as input to Q*'
- 'Most of Google's quality signal is derived from the webpage itself' (PageRank important but not dominant)
- HJ Kim: PageRank_NS feeds into NSR (Normalized Site Rank) computation
- PageRank is one of 'over 100 raw signals' (Nayak testimony)
- Q* (Quality) and P* (Popularity) are the two top-level ranking signals. PageRank contributes to Q*
- HJ Kim (PXR0356, Feb 2025): Confirmed PageRank is 'a single signal relating to distance from a known good source,' used as 'an input to the Quality score.' Reinforces that PageRank is one component of Q*, not a standalone ranking factor

### Reasonable Surfer: What Affects Link Value (exploit)
- Font size: Larger anchor text = higher click probability = more PageRank passed
- Position: Links near top of page carry more weight. Footer/sidebar links devalued
- Color/styling: Differently colored links, bold/italic styling affects perceived importance
- Context: Words surrounding the anchor text affect link weight
- Topical relevance: Anchor topic matching page topic increases weight
- Click data: Actual user click frequency on a link affects its PageRank flow
- Internal vs external: Same-domain links weighted differently from cross-domain
- Outbound count: Fewer outbound links = more PageRank per link (dilution effect)
- Yandex cross-reference: Yandex's FI_PAGE_RANK_BONUS applies dynamically to almost all 2+ word queries, giving links additional weight for less-frequent search terms. This suggests Google may similarly weight link authority differently by query length: for short head terms, brand/click signals dominate; for long-tail, links may matter more. Non-commercial link ratio is explicitly tracked in Yandex (confirming IndyRank's editorial independence concept)

### Classic PageRank Formula (Patent)
Each page's PageRank equals the probability that a random web surfer lands on it. With probability d (0.85), the surfer follows a random link. With probability (1-d), the surfer teleports to any random page. PageRank is computed iteratively until convergence, corresponding to the principal eigenvector of the normalized link matrix.
Formula: PR(A) = (1-d)/N + d * (
  PR(T1)/C(T1) +
  PR(T2)/C(T2) + ... +
  PR(Tn)/C(Tn)
)
// d = 0.85 (damping factor)
// C(Ti) = outbound links from page Ti
// Iterative until convergence (eigenvector)
Patents: US6285999
Affects: Affected by: inbound link count, linking page quality, outbound link dilution, link graph structure

### Reasonable Surfer Model (Patent)
Replaces the random surfer (equal probability of clicking any link) with a reasonable surfer (varying probabilities based on link features). Links pass different amounts of PageRank based on the probability a real user would click them. Factors include font size, position, color, anchor text, context, topical relevance, and actual click data.
Formula: LinkWeight(link) = P_click(
  font_size, position,
  color, anchor_text,
  context, topical_match,
  actual_clicks
)
PR_passed = PR(source) * LinkWeight / sum(weights)
// Not all links are equal
Patents: US7716225, US8117209
Affects: Affected by: link prominence, position, styling, topical relevance, actual user click behavior

### PageRank_NS (Nearest Seed) (Patent+Leak)
The production PageRank variant. Instead of pure link-flow, measures shortest distance from diversified trusted seed pages to each page in the graph. Each topic/niche has its own seed set. Pages closer to seeds rank higher. This is NOT the same as TrustRank (which propagates trust like classic PageRank). This is a distance-based algorithm.
Formula: PageRankNS(page) =
  min_distance(
    page,
    seed_set(topic)
  )
// Shorter distance = higher authority
// Topic-specific seed sets (medical ≠ sports)
// Feeds into Q* and NSR computation
Patents: US9165040
Affects: Affected by: link path to seed sites, seed set selection, link quality, topical alignment

## Pipeline
- **Link Graph Construction**: Trawler crawls web, Alexandria builds link graph. Billions of pages, trillions of links
- **Seed Selection**: Diversified trusted seed sets selected per topic/niche (e.g., NYT, Wikipedia, .gov sites)
- **Distance Computation**: PageRank_NS computed as shortest distance from seeds, weighted by Reasonable Surfer link probabilities
- **Homepage Propagation**: homepagePagerankNs computed first. Acts as trust anchor for all pages on domain
- **IndyRank Assessment**: Backlink independence measured. Many unrelated sources > cluster of related sites
- **Feed into Q***: PageRank_NS and related signals feed into Q* (Quality score) alongside content and spam signals

## Timeline
- 1998: Original PageRank - Larry Page files US6285999 at Stanford. Random surfer model with d=0.85 damping factor.
- 2004: Reasonable Surfer - US7716225 filed. Links weighted by click probability. Font size, position, context all matter.
- 2006: Seed-Based Distance - US9165040 filed. PageRank_NS measures distance from trusted seeds, not raw link flow.
- 2016: Toolbar PageRank Hidden - ToolbarPageRank removed from public view. Still computed internally.
- 2019: Patent Expires - Original PageRank patent (US6285999) expires September 2019. Algorithm continues internally.
- 2023: Yandex Leak Cross-Reference - Yandex leak confirms active PageRank with BM25 for anchor+body relevance (not TF*IDF). FI_LINK_RELEV evaluates link relevance per-query (dynamic, not static). FI_PAGE_RANK_BONUS applies extra link authority weight for 2+ word queries, suggesting link importance is query-length dependent. Homepage links weighted higher than deep page links. Top-100 PageRank sites carry disproportionate weight. Wikipedia links confirmed as a strong quality signal.
- 2024: 7 Variants Revealed - API leak reveals 7 active PageRank variants: pagerank, pagerank_ns, homepage, toolbar, raw, v2, firstCoverage.
- 2025: DOJ: Input to Q* - Nayak confirms PageRank is 'a single signal' feeding into Q*. One of 'over 100 raw signals.'
- 2025: HJ Kim Confirms Distance-Based Definition (PXR0356) - HJ Kim (DOJ Remedies Exhibit PXR0356, Feb 2025): PageRank is 'a single signal relating to distance from a known good source,' used as 'an input to the Quality score.' Confirms PageRank_NS (distance from seeds) is the production variant.
