# NSR: Normalized Site Rank

## Key Insight
NSR is built on sitechunks, not individual pages, allowing Google to score entire domains as units. The system uses site2Vec embeddings to understand what a site is "about" and measures siteFocusScore (topical concentration). Sites that stray from their core topic via siteRadius get penalized. Chrome's directFrac (direct traffic percentage) feeds into authority, meaning brand recognition directly impacts rankings.

### Signals Confirmed (leak)
- siteFocusScore - How concentrated a site is on specific topics (topical authority)
- site2Vec - Site-wide semantic vector for topical understanding
- siteAuthority / site_pr - Site-level PageRank and composite trust score
- chardScores - Content quality prediction scores across the site
- vlq / vlqNsr - Very Low Quality score, severe quality penalty indicator
- directFrac - Fraction of direct (type-in) traffic via Chrome data
- siteRadius - How far pages deviate from the site's core topic embedding

### Signals,  Core (doj)
- siteAuthority - Persistent composite site-level trust and reputation score
- NSR - Normalized Site Rank, successor to PageRank for site quality
- predictedDefaultNsr - Baseline quality score from content, behavior, and links
- authorityPromotion - Positive boost for high-authority domains
- unauthoritativeScore - Direct negative signal for lacking authority
- site2vecEmbeddingEncoded - Site-level vector in semantic space
- PageRankSeedDistance - Distance from trusted seed sites in link graph

### 3 Patents (patent)
- Site quality prediction via distributed sitechunk analysis
- Topical authority measurement through embedding similarity
- YMYL-specific scoring models (healthScore, ymylNewsV2Score)
- Codename signals: rhubarb, tofu, keto, ewok for quality prediction
- E-E-A-T via encodedAccountableEntity linking sites to known entities
- smallPersonalSite flag for different ranking treatment of niche sites
- US7085753: Method and system for mapping and searching the Internet and displaying the results in a visual form; US7054651: System and method for providing a site specific location of a device; US5956720: Method and apparatus for web site management

### How NSR Affects Your SEO (exploit)
- Maximize siteFocusScore by staying in your lane: siteFocusScore directly measures topical concentration. Sites that cover a narrow topic deeply score higher than sites that cover everything shallowly. A cooking site that publishes crypto articles dilutes its focus score. If you must cover multiple topics, use separate domains or subdomains that each maintain tight topical focus.
- Monitor siteRadius to prevent topical drift: siteRadius measures how far individual pages deviate from your site's core topic embedding (site2Vec). Pages that stray too far from your site's established semantic center are penalized. Before publishing content on a new topic, verify it falls within a reasonable radius of your existing content themes. Expand gradually rather than jumping to unrelated topics.
- Build directFrac through brand recognition: directFrac measures the fraction of direct/type-in traffic from Chrome browser data, feeding directly into NSR computation. This means brand-building campaigns, memorable domain names, and repeat visitor strategies have a concrete ranking impact. Newsletters, social media presence, and offline marketing that drive users to type your URL directly into Chrome improve this signal.
- Maintain quality consistency across sitechunks: NSR scores sites via sitechunks (URL pattern groups scored independently). One low-quality section (/forum/ or /user-content/) can drag down your entire domain's NSR. Audit content quality by URL pattern. Noindex or remove low-quality sections rather than letting them dilute your site-wide score. Every section must meet the same quality bar.
- Avoid vlq (Very Low Quality) classification at all costs: vlq and vlqNsr are severe quality penalty indicators that cap your NSR score. Once triggered, the ceiling on your site's quality score limits rankings across all pages. Thin content, doorway pages, auto-generated pages, and excessive affiliate content are common triggers. Prune aggressively before vlq is applied, because recovery after classification is slow.
- Leverage predictedDefaultNsr for new sites strategically: New sites receive a predictedDefaultNsr baseline score derived from content, behavior, and link signals before enough site-specific data accumulates. Launch with your highest-quality content first, earn initial authoritative links, and drive engaged traffic early. The predicted default score sets your starting position and influences how quickly you earn full NSR evaluation.
- Use encodedAccountableEntity for E-E-A-T signals: NSR includes E-E-A-T measurement via encodedAccountableEntity, which links sites to known entities. Ensure your site is connected to a recognized entity in Google's Knowledge Graph. Consistent author profiles, organizational schema markup, Wikipedia presence, and verifiable credentials help establish this entity connection.
- Yandex cross-reference: traffic source diversity and domain-wide ranking velocity: Yandex tracks organic traffic percentage and penalizes sites that rely solely on one traffic channel. FI_URL_DOMAIN_FRACTION (+0.564, the highest positive factor in Yandex) rewards domains whose names match query terms. Yandex also computes 'average domain ranking across queries' as a quality proxy: if a domain ranks well across many queries, it is likely authoritative. This suggests Google's NSR may similarly encode a 'domain ranking velocity' metric, and traffic source diversity (direct + social + referral + organic) could be an implicit NSR input via Chrome data.

### NSR (Leak+DOJ)
Host-level quality score computed via sitechunks (URL pattern groups). Successor to raw PageRank for site-level assessment. Normalizes quality scores across the web.
Formula: NSR(host) = normalize(
sitechunkScores[], site_pr, directFrac, chardScores
)  // normalized 0.0-1.0 across the web
if vlq > threshold: NSR = clamp(NSR, 0, vlqCap)
// sitechunks = URL pattern groups scored independently
Patents: US9165040 (link graph distances), US9400849 (scalable short paths)
Affects: Affected by: site-wide content quality consistency across URL patterns. Inconsistent quality across sections hurts.

### site_pr (Leak)
Aggregated PageRank at domain level. Combines page-level authority into a single domain score. Part of QualityNsrNsrData module.
Formula: site_pr(domain) = aggregate(
pageRank[page] for page in domain.pages
)  // QualityNsrNsrData module
// all pages contribute; authoritative pages lift entire domain
Patents: US9165040 (link graph distances)
Affects: Affected by: domain-wide link authority. All pages contribute. More authoritative pages lift the whole domain.

### predictedDefaultNsr (DOJ)
Default quality score derived from content, behavior, and links. Used as baseline when site-specific data is sparse.
Formula: predictedDefaultNsr(site) = predict(
contentSignals, behaviorSignals, linkSignals
)  // baseline when site-specific data is sparse
if site.isNew: NSR = predictedDefaultNsr
// new sites inherit this until enough data accumulates
Affects: Affected by: foundational site quality signals. New sites start with this predicted score.

### chardScores (Leak)
Quality prediction scores based on content analysis. Part of QualityNsrNsrData. Separate from behavioral signals.
Formula: chardScores(site) = contentPredict(
textQuality[], structureDepth, originalitySignals
)  // content-only, no behavioral input
// feeds into NSR alongside behavioral and link signals
Affects: Affected by: content quality patterns. Consistent, well-structured content across the site.

## Pipeline
- **Sitechunks**: Domain divided into content chunks for distributed scoring
- **Content Analysis**: chardScores, gibberishScores, racterScores, articleScoreV2
- **Topical Modeling**: site2Vec, siteFocusScore, siteRadius, TopicEmbedding
- **Authority Signals**: site_pr, SiteLinkIn/Out, directFrac, PageRankSeedDistance
- **NSR Score**: Normalized Site Rank with variance, VLQ flags, YMYL adjustments

## Timeline
- 2004: PageRank Era - Google relies on page-level PageRank. No site-level quality system exists yet.
- 2011: Panda Algorithm - First site-level quality algorithm. Penalizes thin content farms. Seeds the idea of site-wide scoring.
- 2015: Site Quality Rater Guidelines - E-A-T framework formalized. Human raters evaluate site-level expertise, authority, trust.
- 2018: NSR System Emerges - Normalized Site Rank replaces PageRank as the primary site-level quality signal. Operates via sitechunks.
- 2023: Yandex Leak Cross-Reference - Yandex's FI_PAGE_QUALITY_HOST and FI_REG_HOST_RANK confirm site-level quality scoring parallel to NSR. FI_URL_DOMAIN_FRACTION (+0.564) is the highest positive factor in Yandex's system, rewarding domain-query lexical matching. Unique visitor count, direct traffic percentage, and average domain ranking across all queries serve as quality proxies. Yandex tracks organic traffic percentage, suggesting traffic source diversity (not just directFrac) may be a Google signal. Metrika counter installation acts as a legitimacy signal, analogous to Chrome data integration.
- 2024: API Leak: 44 NSR Signals - siteFocusScore, site2Vec, siteAuthority, chardScores, vlq, rhubarb, tofu, keto, ewok and 35 more confirmed.
- 2024: DOJ Trial: Site Authority Confirmed - siteAuthority, authorityPromotion, unauthoritativeScore, PageRankSeedDistance, site2vecEmbedding revealed.

## Contradictions
- Public (Google Public): We don't have a domain authority score. We evaluate pages individually.
  Internal (API Leak + DOJ): NSR is literally Normalized Site Rank, a domain-level quality score. siteAuthority, authorityPromotion, and unauthoritativeScore operate at the site level, not page level.
- Public (Google Public): Topical authority is not a ranking factor. Just create great content.
  Internal (API Leak): siteFocusScore directly measures topical concentration. siteRadius penalizes pages that deviate from the site's core topic embedding. site2Vec creates topical fingerprints.
- Public (Google Public): We don't use Chrome data for ranking.
  Internal (API Leak): directFrac measures the fraction of direct/type-in traffic from Chrome browser data. This feeds directly into NSR site authority computation.
