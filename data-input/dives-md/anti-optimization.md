# Anti-Optimization: Threshold Detection & Pre-Ranking Exclusion

## Key Insight
The Yandex leak revealed a critical architectural distinction: 39 anti-SEO factors operate at the initial retrieval stage (L1), excluding pages from candidate lists before neural re-ranking (L2/L3). This is not demotion; it is pre-ranking exclusion. A page that triggers these hard thresholds never enters the ranking pipeline. If Google's Mustang applies similar hard filters during initial candidate selection from the 400B+ document index, heavily over-optimized pages may be invisible in search results no matter how good their content. Google's API leak confirms multiple over-optimization signals: keywordStuffingScore, phraseAnchorSpamPenalty, exactMatchDomainDemotion, and the binary spamRank (0 or 65535). The Yandex evidence suggests these may include hard exclusion thresholds, not just gradual penalties.

### Google API Leak: Over-Optimization Signals (leak)
- keywordStuffingScore: Keyword density violation measurement. Confirmed in both API leak and DOJ trial
- phraseAnchorSpamPenalty: Combined demotion for spammy anchor text patterns. Tracks count, days, rate, and fraction
- exactMatchDomainDemotion: Explicit penalty for manipulative exact-match keyword domains
- anchorMismatchDemotion: Penalty when inbound anchor text does not match page content topic
- spamRank: Binary 0 or 65535 for linking to known spam sites. No middle ground
- spamBrainTotalDocSpamScore: Overall spam probability on 0-1 scale from SpamBrain neural network
- GibberishScore: Detection of nonsensical or machine-generated text (in CompressedQualitySignals)
- IsAnchorBayesSpam: Bayesian classifier detecting unnatural anchor text distribution patterns
- DocLevelSpamScore: Page-specific manipulation assessment

### DOJ Trial: Spam Architecture (doj)
- SpamBrain: Google's primary spam detection neural network. Uses link graph patterns, content analysis, and behavioral signals
- hostAge / HostAgeSandbox: New domains sandboxed 3-6 months at serving time. Hard threshold on domain age
- Penguin: Integrated into core algorithm since 2015. Link spam penalties applied in real-time crawl cycles
- Mustang initial retrieval: Selects ~10K candidates from 400B+ index. If over-optimization filters apply here, excluded pages never reach ranking
- Pandu Nayak confirmed ranking operates in stages: initial retrieval (Mustang), first-pass scoring (Ascorer), neural re-ranking, Twiddler adjustments

### UPX0204: Ranking for Research (Eric Lehman, Nov 2018) (doj)
- 'Attempts to manipulate search results are continuous, sophisticated, and well-funded' - Internal acknowledgment of the scale of SEO manipulation threat
- 'Information about how search works should remain need-to-know' - Explicit secrecy policy around ranking mechanisms
- 'Keep talk about how search works on a need-to-know basis. Everything we leak will be used against us by SEOs, patent trolls, competitors' - Direct admission that SEO industry exploits any disclosed information
- 'All emails involving people in Search are likely to be retained in connection with multiple, ongoing lawsuits' - Legal retention awareness affecting internal communication
- 'Internal communications have been repeatedly leaked to the press and cast in a bad light' - Acknowledgment of internal leak history

### PXR0356: Reverse Engineering Vulnerability (HJ Kim, Feb 2025) (doj)
- 'Finding the correct edges for these adjustments is difficult, but would be easy to reverse engineer and copy from looking at the data' - Ranking adjustments are fragile and copiable
- Click log exposure risk: If competitors see click logs, 'they have a notion of authority for a given site' - Click data reveals authority signals
- Q* reverse engineering: Q* 'could be easily reverse engineered because Q is largely static' - Quality signal acknowledged as vulnerable to reverse engineering

### 2 Patents (patent)
- Link spam detection via derivative analysis - Patent US7509344 (Google, filed 2004): Detects link manipulation by analyzing PageRank derivatives with respect to coupling factor c. Transition matrix A(c) = [cP + (1-c)E]^T. Derivative x'(c) = (I-cP^T)^(-1)(P-E)^T x(c). Link farms produce large negative normalized derivatives (rank inflated beyond legitimate authority). Web rings produce large positive derivatives. Normalized derivative = x'(c)/x(c) isolates manipulation signal independent of absolute rank
- Phrase-based spam detection - Patent US8078629 (Google, filed 2006): Indexes documents by n-gram phrase fingerprints. Identifies spam through anomalous phrase co-occurrence patterns: documents sharing unusual phrase combinations across unrelated topics. Phrase distribution analysis flags content farms, article spinning, and keyword-stuffed pages. Co-occurrence graph reveals coordinated spam networks producing content with similar phrase templates

### Yandex Leak: Anti-SEO Architecture (exploit)
- 315 anti-SEO threshold factors: When optimization metrics exceed defined limits, pages are flagged as potentially manipulated
- 39 anti-SEO factors at L1 (initial retrieval): Can exclude over-optimized pages from posting lists before neural ranking. This is pre-ranking exclusion, not demotion
- FI_ADV (weight: -0.251): Heaviest single negative factor in the entire system. Advertising presence/density penalty
- FI_COMM_LINKS_SEO_HOSTS (weight: -0.181): Commercial/SEO anchor text penalty. Links from known SEO networks penalized
- FI_SPAM_KARMA: Whois-based spam probability. Domain registration data used to assess manipulation likelihood
- PF filter: Click-through rate manipulation detection via IP similarity monitoring. Identifies artificial click patterns
- Nepot filter (since 2005): Link manipulation detection for reciprocal and paid link schemes
- Minusinsk update (2015): PBN and paid link detection system targeting private blog networks
- Tiered architecture: L1 initial retrieval, L2 re-ranking, L3 neural re-ranking. Anti-SEO thresholds can exclude at any tier

### How Anti-Optimization Affects Your SEO (exploit)
- Pre-ranking exclusion is the worst possible penalty: If Google applies Yandex-style hard filters at the Mustang retrieval stage, over-optimized pages may never enter the ranking pipeline. Unlike a demotion (which still shows you at position 50+), exclusion means your page is not a candidate at all. Recovery requires passing threshold checks, which is harder than improving quality scores. The first step in any penalty diagnosis should be checking whether the page appears in the index at all, not just its ranking position.
- Threshold-based detection means there are hard limits, not gradual curves: The Yandex architecture reveals 315 threshold-based factors (not continuous curves). This means there are specific keyword density levels, anchor text concentration ratios, and link velocity rates that trigger hard flags. You do not gradually lose ranking as you over-optimize; you are fine until you cross a threshold, then the penalty is sudden and severe. Staying well below suspected thresholds is safer than optimizing to the edge.
- Advertising density faces the steepest penalty available: Yandex's FI_ADV (-0.251) is the single heaviest negative factor across 17,854 factors. Google's adsDensityInterstitialViolationStrength (0-1000 scale) and clutterScore operate similarly. If ad density exceeds thresholds, the penalty is disproportionately harsh. Reducing ads from 60% to 30% of viewport may yield more ranking improvement than any amount of content optimization.
- Commercial anchor text concentration is a distinct penalty: Yandex's FI_COMM_LINKS_SEO_HOSTS (-0.181, third heaviest negative factor) penalizes links from sites with commercial/SEO anchor text patterns. Google's phraseAnchorSpamPenalty and anchorMismatchDemotion track similar patterns. If more than a threshold percentage of your inbound anchors use commercial phrases, the penalty triggers regardless of link quality. Aim for 70%+ branded/generic anchors.
- Binary signals mean one violation can flip your status: Google's spamRank is binary (0 or 65535) with no middle ground. A single outbound link to a known spam site flips the score. Yandex's threshold factors behave similarly: crossing any single threshold can trigger the flag. Audit all outbound links, user-generated content, and comment sections regularly. Use nofollow on any link you cannot vouch for.
- Domain registration data feeds spam probability: Yandex's FI_SPAM_KARMA uses Whois data to assess spam likelihood. Google's hostAge sandbox (3-6 months) operates on similar principles. Short domain registrations (1 year), frequent nameserver changes, and DNS instability increase spam probability. Register domains for 3-5+ years, maintain stable DNS records, and avoid hosting on infrastructure associated with spam networks.
- CTR manipulation detection uses IP similarity: Yandex's PF filter monitors IP patterns behind clicks to detect artificial CTR inflation. Google's squashedClicks and NavBoost manipulation detection serve the same function. Do not use click bots, click farms, or any service that generates artificial clicks. Even sophisticated click manipulation leaves IP distribution patterns that detection systems identify.
- Distinguish between demotion and exclusion in your diagnosis: If your page ranks at position 50-100, it entered the pipeline but was demoted (content/quality issue). If your page does not appear in search results despite being indexed, it may have been excluded at the retrieval stage (over-optimization issue). The fixes are different: demotion requires improving quality signals; exclusion requires reducing optimization signals below threshold limits.

### Pre-Ranking Exclusion (Hypothesis from Yandex) (Yandex Leak: L1 anti-SEO filtering)
Yandex applies 39 anti-SEO factors at the initial retrieval stage (L1), which can exclude pages from posting lists before they reach the neural re-ranking pipeline. If a page exceeds any of these thresholds, it is removed from the candidate set. Google's Mustang selects ~10K candidates from 400B+ documents. If similar hard filters exist in Mustang, over-optimized pages are excluded before Ascorer, SpamBrain, or Twiddlers ever evaluate them.
Formula: MustangFilter(doc) = check(
  anti_seo_thresholds[]
)
if any(threshold_exceeded): EXCLUDE
// Yandex: 39 factors at L1 retrieval stage
// Excluded pages never reach ranking pipeline
// Different from demotion: page is invisible, not low-ranked
Affects: Affected by: keyword density exceeding thresholds, anchor text concentration, link velocity spikes, ad density, and other over-optimization signals. Stay well below suspected limits.

### Over-Optimization Threshold Detection (Yandex Leak + Google API Leak)
Yandex uses 315 threshold-based anti-SEO factors. When any metric exceeds its defined limit, the page is flagged. Google's confirmed signals (keywordStuffingScore, phraseAnchorSpamPenalty, exactMatchDomainDemotion) operate on similar threshold principles. The key insight is that these are hard thresholds, not gradual curves: a page is either below the limit (no penalty) or above it (full penalty applied).
Formula: OverOptScore(doc) = evaluate(
  keywordStuffingScore,
  phraseAnchorSpamFraq,
  anchorMismatchDemotion,
  exactMatchDomainDemotion,
  spamBrainTotalDocSpamScore
)
if any(metric > threshold): apply penalty
// Yandex: 315 threshold-based factors
// Google: multiple confirmed threshold signals
// Penalty is binary at threshold, not gradual
Patents: US9002867 (spam detection), US7533092 (link-based spam detection)
Affects: Affected by: keyword density, anchor text diversity, link velocity, domain age, outbound link quality, ad density. All have hard threshold limits.

### Link Spam Derivative Detection (Patent US7509344 (Google, 2004))
Detects link manipulation by analyzing how a page's rank changes as the coupling factor c varies. The transition matrix A(c) = [cP + (1-c)E]^T defines rank as a function of c. The derivative x'(c) = (I-cP^T)^(-1)(P-E)^T x(c) measures sensitivity to the link graph. Normalizing by rank (x'(c)/x(c)) isolates the manipulation signal. Link farms produce large negative normalized derivatives (rank inflated beyond legitimate authority via concentrated inbound links). Web rings produce large positive derivatives (mutual linking creates artificial rank amplification). Legitimate sites have moderate, stable derivatives.
Formula: A(c) = [cP + (1-c)E]T
// P = link transition matrix, E = teleport

x'(c) = (I - cPT)-1(P-E)T x(c)

NormalizedDeriv = x'(c) / x(c)
// large negative = link farm (rank inflated)
// large positive = web ring (mutual linking)
// moderate = legitimate site
Patent: US7509344 (Link spam detection, 2004)
Affects: Affected by: inbound link concentration from few sources, reciprocal linking patterns, link farm participation. Derivative analysis reveals manipulation independent of absolute rank.

### Ad Density Penalty (Yandex Leak + Google API Leak)
Yandex's FI_ADV (weight: -0.251) is the heaviest single negative factor in their entire 17,854-factor system. Google's adsDensityInterstitialViolationStrength operates on a 0-1000 scale, with clutterScore adding a site-level penalty. Both systems treat advertising density as an extreme negative signal, with penalties disproportionate to most other factors.
Formula: AdPenalty(page) = max(
  adsDensityViolation(0-1000),
  clutterScore(site_level)
)
// Yandex FI_ADV: -0.251 (heaviest negative factor)
// Google: 0-1000 scale + site-level clutter
// Disproportionate penalty vs other signals
Affects: Affected by: above-the-fold ad density, interstitial popups, auto-play video ads. Reducing ad density may yield more ranking improvement than content optimization.

## Pipeline
- **Crawl-Time Signals**: Keyword density, content structure, link patterns captured during crawl (Trawler/Alexandria)
- **Pre-Ranking Filters (L1)**: Hard threshold checks on over-optimization metrics. Exceeding limits excludes page from candidate set (Yandex: 39 L1 factors)
- **Initial Scoring (Ascorer)**: keywordStuffingScore, anchorMismatchDemotion, exactMatchDomainDemotion applied during first-pass ranking
- **SpamBrain Neural Analysis**: Neural network evaluates link graph patterns, content quality, behavioral signals. Outputs spamBrainTotalDocSpamScore (0-1)
- **Bayesian Classifiers**: IsAnchorBayesSpam, phraseAnchorSpam suite detect unnatural patterns. Velocity, concentration, and distribution analysis
- **Action: Demotion or Exclusion**: Gradual demotion (post-ranking) or hard exclusion (pre-ranking). Different recovery paths for each

## Timeline
- 2003: Early Spam Filters - Google files first patents on link spam detection and content quality filtering.
- 2004: Link Spam Derivative Patent (US7509344) - Google patents link manipulation detection via PageRank derivative analysis. Transition matrix A(c) = [cP + (1-c)E]^T. Normalized derivative x'(c)/x(c) reveals link farms (large negative) and web rings (large positive). Mathematical foundation for link spam detection.
- 2005: Yandex Nepot Filter - Yandex introduces Nepot filter for link manipulation detection (reciprocal and paid links).
- 2006: Phrase-Based Spam Patent (US8078629) - Google patents phrase-based spam detection using n-gram fingerprints. Documents indexed by phrase co-occurrence patterns. Anomalous phrase distributions reveal content farms, article spinning, and keyword stuffing. Co-occurrence graphs identify coordinated spam networks.
- 2012: Penguin Algorithm - Major link spam update targeting manipulative link building and exact-match anchor text abuse.
- 2015: Yandex Minusinsk Update - Yandex introduces PBN and paid link detection system targeting private blog networks.
- 2018: SpamBrain Launches - Google announces AI-based spam detection. Neural network for link spam pattern recognition at scale.
- 2023: Yandex Leak: Pre-Ranking Exclusion Revealed - Yandex source code reveals 315 anti-SEO threshold factors, with 39 operating at L1 (initial retrieval). Pages exceeding thresholds excluded before neural re-ranking. FI_ADV (-0.251) is the heaviest negative factor. FI_COMM_LINKS_SEO_HOSTS (-0.181) penalizes SEO anchor patterns.
- 2024: API Leak: Spam Signals Confirmed - keywordStuffingScore, phraseAnchorSpamPenalty, spamRank (binary 0/65535), exactMatchDomainDemotion, anchorMismatchDemotion confirmed.
- 2024: DOJ Trial: Sandbox and GibberishScore - hostAge sandboxes new domains 3-6 months. GibberishScore detects AI-generated content. scamness scored 0-1023.
- 2018: Ranking for Research: Need-to-Know Policy (UPX0204) - Eric Lehman (Nov 2018): 'Attempts to manipulate search results are continuous, sophisticated, and well-funded.' 'Everything we leak will be used against us by SEOs, patent trolls, competitors.' Explicit internal policy to keep ranking information on a need-to-know basis.
- 2025: Q* Reverse Engineering Vulnerability (PXR0356) - HJ Kim (Feb 2025): Q* 'could be easily reverse engineered because Q is largely static.' Click logs would reveal 'a notion of authority for a given site.' Ranking adjustments 'easy to reverse engineer and copy from looking at the data.'

## Contradictions
- Public (Google Public): There is no specific keyword density that triggers a penalty. Just write naturally.
  Internal (API Leak + Yandex Leak): keywordStuffingScore is a confirmed threshold-based signal. Yandex uses 315 threshold-based anti-SEO factors with hard limits. Exceeding the threshold triggers immediate penalty, not gradual demotion.
- Public (Google Public): We don't have a sandbox for new domains.
  Internal (API Leak + DOJ): hostAge and HostAgeSandbox explicitly gate new domain visibility for 3-6 months. Domain registration data (Whois) feeds into spam probability scoring.
- Public (Google Public): We just promote better content. We don't penalize sites.
  Internal (API Leak): exactMatchDomainDemotion, anchorMismatchDemotion, phraseAnchorSpamPenalty, keywordStuffingScore are all explicit demotion signals. The Yandex evidence suggests even harder penalties exist: pre-ranking exclusion at the retrieval stage.
- Public (Google Public): We are transparent about how Search works. We publish extensive documentation on ranking factors and algorithm updates.
  Internal (DOJ Trial Exhibit UPX0204 (Eric Lehman, Nov 2018)): 'Information about how search works should remain need-to-know.' 'Everything we leak will be used against us by SEOs, patent trolls, competitors.' Internal communications explicitly advocate secrecy about ranking mechanics.
- Public (Google Public): Our ranking systems are complex and cannot be easily gamed or reverse-engineered.
  Internal (DOJ Remedies Exhibit PXR0356 (HJ Kim, Feb 2025)): Q* 'could be easily reverse engineered because Q is largely static.' Click logs would expose 'a notion of authority for a given site.' Ranking adjustments are 'easy to reverse engineer and copy from looking at the data.'
