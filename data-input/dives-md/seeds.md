# Seed Sites: The Foundation of Google's Trust System

## Key Insight
Google's modern PageRank does not count links. It measures distance from trusted seed sites. Pages closer to seed sites are more authoritative. Pages farther away are less trustworthy. Seed sets are organized by topic, with different niches having distinct seed sets. This was confirmed under oath by Google engineer HJ Kim. Patent US20100114858 reveals the exact 5-step filtering process Google uses to select seeds: (1) host importance threshold by market, (2) expected yield of new documents, (3) document quality filter (spam, porn, corrupted forums), (4) market/geographic distribution, (5) topical diversity across languages and fields.

### 8 Patents (patent)
- US9165040 - Ranking using distances in web-link graph: Core distance algorithm. Defines link length L(q→p) = α + log(outbound_links). Uses k-nearest seeds (k=3-6). Score = e^(-D) exponential decay. Names Google Directory and NYT as example seeds.
- US7603350 - Search result ranking based on trust (Ramanathan Guha): Google's version of TrustRank. Expert labelers manually identify reputable seed pages. Trust propagates through link graph. Different from Yahoo TrustRank in that it uses distance, not iterative propagation.
- US9400849 - Scalable short paths in web link network: Topic-specific seed shards for computational efficiency. Medical has medical seeds, sports has sports seeds. Enables niche-specific authority.
- US8762394 - Obtaining recommendations from trusted sources (Trust buttons): User trust signals supplement seed propagation. Trust is transitive from trusted sources.
- US20100114858 - Host-based seed selection algorithm: The selection criteria patent. 5-step filtering: importance threshold, yield analysis, quality filter, market distribution, topical diversity. Selects best document per qualifying host.
- US9558233: Determining a quality measure for a resource; US11106712: Systems and methods for measuring the semantic relevance of keywords; US7606798: Methods and systems for improving a search ranking using location awareness

### 3 Production Signals Confirmed (leak)
- PageRankNS (CompositeDoc) - Nearest Seed PageRank. Core Tier 1 signal. 40+ patent cross-references. Measures authority based on proximity to trusted seed pages in your topic shard.
- nearestSeed (PerDocData) - Localized network relevance based on topical proximity to seed pages. Tier 2 signal. Confirms topic-specific seeding is active in production.
- siteAuthority (CompressedQualitySignals) - Site-level trust score from quality_nsr.SiteAuthority. Aggregates PageRankNS + NSR + trust signals. DOJ-confirmed. Google denied its existence (2016-2019) while it was active internally.

### Sworn Testimony Confirmation (doj)
- HJ Kim (sworn deposition): Modern PageRank measures distance from trusted seeds, not raw link count. Core signal in NSR/PerDocData. Different niches have different seed pages. Seed site distance is a distinct signal from raw PageRank.
- Pandu Nayak (VP Search): PageRank is input to Q* quality scoring. Q* uses seed distance as input. Page quality is 'mostly static' and represents 'site-wide trustworthiness.'
- siteAuthority - Persistent composite site-level trust score representing domain reputation. Google publicly denied 'domain authority' (2016-2019) while this existed.
- Q* - 0-1 site-wide quality score. Below 0.4 = ineligible for featured snippets and rich results. Uses seed distance as input. NOT ML-based (confirmed HJ Kim).

### How Seed Distance Affects Your Rankings (exploit)
- Minimize link chain distance: Each hop accumulates distance. A direct link from a seed-adjacent site (distance 1) is worth far more than being 5 hops away through a chain of blogs.
- Target sites close to seeds: Major news publishers, .gov, .edu, Wikipedia are confirmed/likely seeds. Getting featured in NYT or cited by a university page puts you 1-2 hops from seeds.
- Outbound link count matters: Links from pages with fewer outbound links create shorter distances. A homepage link from a site with 10 outlinks passes more 'distance value' than a blogroll with 200 links (link length = α + log(outlinks)).
- Topic relevance is critical: Seeds are sharded by niche. A medical seed has no relevance for your sports site. Earn links from authoritative sites IN your niche.
- Site-wide quality compounds: siteAuthority operates at domain level. One strong page doesn't fix a weak domain. Build consistent quality across your entire site.
- Digital PR is the strategy: Newsworthy content earns links from institutional sites (news, universities, government) that are closest to seed sets. This is the most direct path to reducing seed distance.
- PBNs are far from seeds: Private blog networks are typically many hops from seed sites and lack IndyRank (link independence). The exponential decay means distant links carry negligible value.
- Brand queries help: queriesForWhichOfficial signal boosts sites that users search for by name. Strong brands naturally accumulate seed-adjacent links.

### Link Distance Formula (US9165040) (Patent (core algorithm))
Each link in the web graph has a 'length' determined by the source page's outbound link count. More outbound links = longer link length (weaker per-link value). The distance from a seed to any page is the sum of link lengths along the shortest path. This is NOT iterative trust propagation; it's a shortest-path computation.
Formula: // Link length between pages q→p
L(q → p) = α + log(|q|out)
// α = -log(d), d = damping factor (≈0.85)
// |q|_out = outbound links from source page q

// Distance from seed s_i to page p
Di(p) = min Σ L(qj → qj+1)
// along shortest path: s_i → q1 → q2 → ... → p

// Final score: k-nearest seeds (k=3 to 6)
D(p) = k-th minimum of Di(p)
Score(p) = e-D(p)
// Exponential decay: shorter distance = higher score
Patent: US9165040 (Producing a ranking for pages using distances in a web-link graph)
Affects: Affected by: number of hops to nearest seed, outbound link count of intermediary pages, topic relevance of seed shard. Each additional hop and each additional outlink on intermediary pages increases distance.

### K-Nearest Seeds Algorithm (Patent US9165040)
Not all seeds matter for a given page. The algorithm computes distance to ALL seeds, then takes only the k-th minimum (k=3 to 6). This prevents anomalies where a page happens to be very close to a single seed but far from all others. It also enables topic-specific scoring: a medical page's score depends on its distance to the closest 3-6 medical seeds, not to sports seeds.
Formula: // For each seed s_i with optional weight w_i
Di(s_i) = -log(w_i)
// Seed weight affects starting distance
// Higher weight w_i → shorter initial distance

// Final ranking uses k-th minimum
D(p) = sort(D1(p), D2(p), ..., Dn(p))[k]
// k=3: use 3rd-closest seed (suppresses outliers)
// k=6: more conservative, requires multiple nearby seeds
Patent: US9165040, US9400849 (scalable short paths, topic sharding)
Affects: Affected by: having multiple seed-adjacent sources (not just one), topic relevance of seeds to your content, seed weighting values

### Seed Selection Criteria (US20100114858) (Patent (5-step algorithm))
Google selects seeds through a 5-step filtering process. Starting from all candidate hosts, each step eliminates unqualified hosts until only the best remain. The process is market-aware (different thresholds per region) and quality-conscious (spam/porn/forum corruption filtered out).
Formula: // Step 1: Market importance threshold
filter hosts where PageRank(root) threshold[market]

// Step 2: Expected yield of new documents
filter hosts where yield(host) min_yield
// yield = P(discovering new pages via outlinks)

// Step 3: Document quality filter
filter hosts with spam OR porn OR corrupted_forums

// Step 4: Market/geographic distribution
allocate seeds proportionally by market value

// Step 5: Best document per host
select highest_quality_page from each host
Patent: US20100114858 (Host-based seed selection algorithm for web crawlers)
Affects: Affected by: host PageRank, outlink quality, spam reputation, geographic market, topical coverage

### PageRankNS (Production Signal) (DOJ (HJ Kim deposition) + API Leak)
The production version of seed-based distance ranking. Computes link graph distance from topic-specific trusted seed pages. Shorter path = higher score. Seed sets are sharded by topic, so different niches have different seed pages. Value decays exponentially with each hop.
Formula: PageRankNS(url) = e-D
// D = distance from nearest topic-relevant seeds
// Uses k-nearest seeds (k=3-6)
// Distance = sum of link lengths along shortest path
// Tier 1 core signal, 40+ patent matches
Patents: US9165040, US7603350
Affects: Affected by: link chain distance to topic-relevant seeds. Natural links from trusted, well-connected sources matter more than link volume.

### siteAuthority (DOJ + API Leak)
Persistent composite score at domain level. Converted from quality_nsr.SiteAuthority module, then applied in Q* ranking pipeline. Aggregates PageRankNS, NSR, and other trust signals into a single domain reputation score.
Formula: siteAuthority(domain) = aggregate(
  PageRankNS, NSR, trust_signals
)
// domain-level, shared across all pages on host
// stored in quality_nsr.SiteAuthority module
Patents: US9165040, US9400849
Affects: Affected by: domain-wide quality. All pages share this score. Consistent quality across entire site improves it. Not per-page.

### Q* (Quality Score) (DOJ (Pandu Nayak testimony))
Site-wide, query-independent score on 0-1 scale. Hand-crafted (confirmed NOT ML). Below 0.4 = ineligible for featured snippets and rich results. Operates at subdomain level. Largely static, representing domain trustworthiness. Inputs include seed distance, engagement signals, E-E-A-T indicators.
Formula: Q*(site) = hand_crafted(
  seed_distance, engagement, E-E-A-T
)  // range: 0.0 - 1.0
if Q* < 0.4: block featured_snippets, rich_results
// NOT ML-based (confirmed HJ Kim deposition)
Affects: Affected by: overall site trustworthiness, authority, expertise signals. Changes slowly. The 0.4 threshold is a hard gate for SERP features.

## Pipeline
- **1. Seed Selection (US20100114858)**: 5-step process: (1) Host importance threshold per market, (2) Expected yield of new documents, (3) Quality filter (spam, porn, corrupted forums), (4) Market/geographic distribution, (5) Select best document per qualifying host. Seeds manually reviewed by experts.
- **2. Topic Sharding (US9400849)**: Seeds organized into topic-specific shards. Medical, sports, technology, finance each have distinct seed sets. Enables niche-specific authority scoring.
- **3. Distance Computation (US9165040)**: For each page, compute shortest path to all seeds. Link length = α + log(outlinks). Use k-nearest seeds (k=3-6) for final score. Score = e^(-distance).
- **4. PageRankNS Signal**: Production Tier 1 signal. Per-document seed distance stored in CompositeDoc. nearestSeed (Tier 2) provides topical proximity.
- **5. NSR / siteAuthority**: Aggregated at site level. siteAuthority in quality_nsr combines PageRankNS + NSR + trust signals. Domain-wide reputation score.
- **6. Q* Quality Gate**: 0-1 quality score using seed distance as input. Below 0.4 blocks featured snippets/rich results. Hand-crafted, not ML.
- **7. Seed Tuning**: System periodically reviews seed quality. Per-seed performance evaluated. Seeds can be re-weighted, replaced, or removed based on contribution to result quality.
- **8. Final Ranking**: Seed distance combined with NavBoost (engagement), T* (topicality), P* (popularity), Ascorer, and Twiddlers for final SERP position.

## Timeline
- 2004: TrustRank Paper Published - Stanford/Yahoo: 'Combating Web Spam with TrustRank' introduces seed-based trust propagation. Shows <200 seeds can filter most web spam.
- 2006: Google Trust Rank Filed - US7,603,350 filed by Ramanathan Guha. Trust scores from seed labelers. Google's approach differs from Yahoo: distance-based, not iterative.
- 2006: Link Distance Ranking Patent - US9165040: 'Producing a ranking using distances in a web-link graph.' Defines the mathematical framework: link length = α + log(outlinks), k-nearest seeds, exponential decay.
- 2007: Yahoo Dual TrustRank - US20070112761: parallel trust systems for content and social graphs. Topical TrustRank solves big-site bias by sharding seeds by topic.
- 2009: Google Trust Rank Granted - US7,603,350 granted. Bill Slawski (SEO by the Sea) analyzes differences from Yahoo TrustRank.
- 2010: Seed Selection Algorithm - US20100114858: 5-step filtering criteria for choosing optimal seed sites. Host importance, yield analysis, quality filter, market distribution, topical diversity.
- 2014: Trust Buttons Patent - US8,762,394: explicit trust signals from users supplement seed propagation. Trust is transitive from trusted sources.
- 2015: Scalable Short Paths Patent - US9400849: seed sets divided into topic-specific shards. Enables niche-specific distance computation at web scale.
- 2016-2019: Google Denies Domain Authority - Multiple public statements: 'There is no domain authority metric.' Meanwhile Q* and siteAuthority exist internally using seed distance.
- 2024: Google API Leak - PageRankNS (Tier 1, 40+ patents) and nearestSeed (Tier 2) confirmed in ContentWarehouseAPI. siteAuthority in quality_nsr confirmed.
- 2024-2025: DOJ Trial Testimony - HJ Kim sworn deposition: PageRank measures distance from trusted seed sites, not raw link count. Different niches have different seed pages.

## Contradictions
- Public (Google Public (2016)): "There is no such thing as domain authority that Google would use."
  Internal (Internal Reality): siteAuthority exists as a computed feature in quality_nsr, working alongside Q* quality score. PageRankNS measures seed distance at site level.
- Public (Google Public (2019)): "We don't use domain authority. We evaluate pages individually."
  Internal (DOJ Trial (2024)): HJ Kim testimony: Page quality is 'mostly static' and represents 'site-wide trustworthiness,' not individual page merit. Q* is query-independent.
- Public (Google Public (various)): "PageRank is just one of hundreds of signals and not that important anymore."
  Internal (API Leak (2024)): PageRankNS is a Core Tier 1 signal with 40+ patent cross-references. nearestSeed provides localized topical proximity. Both are in the primary CompositeDoc module.
