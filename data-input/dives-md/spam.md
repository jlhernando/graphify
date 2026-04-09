# SpamBrain: Google's Anti-Spam System

## Key Insight
New domains are sandboxed for 3-6 months via the hostAge attribute, contradicting Google's public claim that domain age doesn't matter. SpamBrain assigns a scamness score (0-1023) for fraud detection, a GibberishScore for AI-generated content, and a binary spamRank (0 or 65535) based on outbound link patterns.

### Signals Confirmed (leak)
- phraseAnchorSpamPenalty - Combined anchor text demotion for spammy link patterns
- spamRank - Binary score (0 or 65535) for linking to known spammers
- spamBrainTotalDocSpamScore - Overall spam probability on 0-1 scale
- keywordStuffingScore - Keyword density violation measurement
- anchorMismatchDemotion - Penalty when inbound links don't match site topic
- exactMatchDomainDemotion - Penalty for manipulative exact-match domains
- phraseAnchorSpamDays - Days to discover 80% of spam phrases (link velocity)
- CrapsIpPrior (packedIpAddress) - IP-level reputation scoring within NavBoost's CRAPS system. packedIpAddress stores network byte order IP string. unscaledIpPriorBadFraction measures IP-level bad-click reputation before linear scaling/offset/min-max transformation. References internal craps-ip-prior.h and craps-penalty.cc. IP block reputation may explain shared hosting ranking disadvantages (Reboot Online experiment by Shai Aharony/Oliver Sissons)
- CrapsDevice.uxTier - Browser capability tier in NavBoost click classification: 1 = modern browsers, 2 = middle tier, 3 = very old browsers. Disproportionate clicks from low-tier browsers (tier 3) may flag automated manipulation, as click bots and CTR tools typically use outdated user agents that map to low uxTier values

### Signals,  Core (doj)
- GibberishScore - Detection of nonsensical or machine-generated text
- DocLevelSpamScore - Page-specific manipulation assessment
- scamness - Fraud likelihood score, scaled 0-1023
- hostAge - Domain age signal, sandboxes new sites 3-6 months
- HostAgeSandbox - Explicit sandbox mechanism for new domains
- IsAnchorBayesSpam - Bayesian classifier for unnatural anchor patterns
- KeywordStuffingScore - Over-optimization detection (confirmed in both sources)

### 1 Patents (patent)
- Penguin algorithm targets manipulative link building patterns
- Cross-domain spam link detection via link graph topology
- SpamBrain neural network for link pattern recognition
- Anchor text distribution analysis across linking domains
- Link velocity measurement (rate of new backlinks over time)
- Outbound link quality propagation (linking to spam = spam signal)
- Anti-reverse-engineering noise - Patent US8612436 (Grundman, filed 2011): Adds controlled random noise to spam scores to prevent reverse engineering. Noise function B(x)=K*(x-x^2)^2 is shaped to be larger near decision boundaries (demotion at 0.6, blocking at 0.8). Enhanced boundary shaping: B'(x)=B(x)*(1+e^(-2000(x-0.6)^2)*0.9)*(1-e^(-5000(x-0.8)^2)*0.9). Three different variation functions for three score subsets. This is why SEO experiments to reverse-engineer spam thresholds produce inconsistent results

### How SpamBrain Affects Your SEO (exploit)
- Audit outbound links aggressively because spamRank is binary: The leak confirmed spamRank is 0 or 65535 with no middle ground. A single outbound link to a known spam site can flip the score from clean to flagged. Regularly crawl your site for broken links, hacked injection links, and user-generated content (comments, forums) that may link to spam domains. Use nofollow on any link you cannot vouch for.
- Diversify anchor text to avoid phraseAnchorSpamPenalty: The leak tracks phraseAnchorSpamCount (spam phrases from unique domains), phraseAnchorSpamDays (velocity: days to discover 80% of spam phrases), and phraseAnchorSpamFraq (fraction of anchors containing spam). If more than a threshold percentage of your inbound anchors use the same commercial phrase, the penalty triggers. Aim for a natural distribution: brand names, URLs, generic phrases, and varied descriptive text.
- Expect a 3-6 month sandbox on new domains and plan accordingly: DOJ testimony confirmed hostAge sandboxes new domains at serving time. Do not expect organic traffic from a new domain in the first 3-6 months regardless of content quality. Use this period to build content depth, earn initial links, and generate engagement signals (social sharing, direct traffic) that help exit the sandbox faster.
- Avoid keyword density thresholds that trigger keywordStuffingScore: Confirmed in both the API leak and DOJ trial, keywordStuffingScore measures over-optimization. There is no safe density percentage. Instead, write naturally and use semantic variations. If you notice the same phrase appearing more than 3-4 times per 1000 words, rewrite using synonyms or restructure sections.
- Ensure inbound link topics match your site to prevent anchorMismatchDemotion: The leak confirmed anchorMismatchDemotion penalizes when inbound links do not match site topic. A cooking blog receiving bulk links from casino or pharmaceutical sites triggers this demotion. If you have been the target of negative SEO or accumulated off-topic links, disavow them proactively.
- Do not register exact-match domains for SEO advantage: exactMatchDomainDemotion is an explicit penalty in the leak. Domains like 'best-running-shoes-2024.com' that exist solely to exploit keyword matching face demotion. Use a brandable domain and build topical authority through content quality instead.
- Human-edit all AI-generated content to avoid GibberishScore flags: DOJ testimony confirmed GibberishScore detects nonsensical and machine-generated text. Raw LLM output with repetitive phrasing, logical inconsistencies, or hallucinated facts can trigger this filter. Every AI-generated piece needs substantive human editing: add original insights, verify facts, restructure for natural flow, and inject first-hand experience.
- Yandex cross-reference: over-optimization can cause pre-ranking exclusion: Yandex applies 39 anti-SEO factors at the initial retrieval stage (L1), which can exclude pages from candidate lists BEFORE neural re-ranking. This is a qualitatively different penalty from post-ranking demotion: the page never enters the ranking pipeline at all. If Google's Mustang applies similar hard filters during initial candidate selection from the 400B+ index, heavily over-optimized pages may be invisible regardless of content quality. The Yandex leak also reveals FI_ADV (-0.251) as the single heaviest negative factor, validating that ad-heavy pages face extreme penalties.

### spamRank (Leak: binary 0 or 65535)
Binary signal (0 or 65535 in 16-bit representation). Measures likelihood that a page links to known spam sites. 0 = clean, 65535 = flagged. Graph-based analysis of outbound link patterns.
Formula: spamRank(page) = graph_analysis(
  outbound_links, known_spam_set
)  // binary: 0 (clean) or 65535 (flagged)
if links_to_spam > 0: spamRank = 65535
// 16-bit representation, no middle ground
Patents: US9002867 (spam detection in web pages)
Affects: Affected by: your outbound link profile. Linking to spam/low-quality sites triggers this. Audit outbound links regularly.

### phraseAnchorSpamPenalty (Leak)
Combined demotion penalty for spammy anchor text patterns. phraseAnchorSpamCount counts spam phrases from unique domains. phraseAnchorSpamDays tracks velocity (days to discover 80% of spam phrases). phraseAnchorSpamFraq measures fraction of all anchors containing spam.
Formula: phraseAnchorSpam(url) = combine(
  spamCount[unique_domains],
  spamDays(days_to_80pct),
  spamFraq(spam_anchors / total_anchors)
)  // velocity + concentration = penalty
if spamFraq > threshold: apply demotion
Patents: US9002867 (spam detection), US9165040 (link graph analysis)
Affects: Affected by: anchor text diversity and naturalness. Avoid exact-match anchor text campaigns. Fast link velocity with repetitive anchors triggers detection.

### GibberishScore (DOJ: nonsensical/machine-generated text detection)
Detects nonsensical or machine-generated text. Quality filter for content coherence. Part of CompressedQualitySignals module in SpamBrain system.
Formula: GibberishScore(page) = coherence_check(
  text_content, language_model
)  // in CompressedQualitySignals module
if score > gibberish_threshold: flag as machine_generated
// feeds into SpamBrain neural network
Patents: US9002867 (spam detection), US8682892 (content quality scoring)
Affects: Affected by: content readability and coherence. AI-generated content that lacks logical structure may trigger this. Human editing and review improves scores.

### hostAge (DOJ: Domain age signal, 3-6 month sandbox)
Domain age signal used to sandbox new sites for 3-6 months at serving time. Newer domains face initial trust limitations until sufficient behavioral data accumulates. Contradicts Google's public denial of a sandbox.
Formula: hostAge(domain) = days_since(
  first_index_date
)  // serving-time evaluation
if hostAge < ~90-180 days: apply sandbox
// exits sandbox as behavioral data accumulates
Patents: US8645362 (resource load times and trust), US9002867 (spam detection)
Affects: Affected by: domain age. New sites will have reduced visibility for 3-6 months regardless of content quality. Building behavioral signals (engagement) helps exit sandbox faster.

### Spam Score Noise (Anti-Reverse-Engineering) (Patent US8612436 (Grundman, Google, 2011))
Google adds controlled random noise to spam scores to prevent SEO practitioners from reverse-engineering spam thresholds through experimentation. The noise function B(x)=K*(x-x^2)^2 peaks near decision boundaries and drops to zero at extremes (clearly spam or clearly clean). Enhanced boundary shaping amplifies noise specifically near the demotion threshold (0.6) and blocking threshold (0.8), making experimentation near boundaries unreliable. Three different variation functions are applied to three score ranges. Final noisy score: S'(z) = S(z) + B(S(z)) * R(z), where R is a random number in [-1, 1].
Formula: B(x) = K × (x - x2)2
// K = L / 0.0625, L = max noise (e.g., 0.1)
// peaks near 0.5, zero at 0 and 1

B'(x) = B(x) ×
  (1 + e-2000(x-0.6)2 × 0.9) ×
  (1 - e-5000(x-0.8)2 × 0.9)
// amplifies noise near demotion (0.6) and blocking (0.8)

S'(z) = S(z) + B(S(z)) × R(z)
// R(z) = random [-1.0, 1.0]
// prevents reverse engineering via experimentation
Patent: US8612436 (Reverse engineering circumvention of spam detection, 2011)
Affects: Affected by: nothing actionable. This is a defensive mechanism explaining why SEO experiments to detect spam thresholds produce inconsistent, unreliable results. The noise is intentionally maximized near decision boundaries.

### CrapsIpPrior (IP Reputation) (Leak: CrapsData protobuf (NavBoost))
IP-level reputation scoring within NavBoost's CRAPS system. packedIpAddress (network byte order string) identifies the IP block associated with click traffic. unscaledIpPriorBadFraction measures the fraction of bad clicks originating from that IP range before linear scaling and normalization. High bad-click fraction from an IP block degrades the quality of click signals from that range. Implemented in craps-ip-prior.h and craps-penalty.cc. This may explain why shared hosting environments (where sites share IP blocks with spam/low-quality neighbors) face ranking disadvantages.
Formula: CrapsIpPrior(ip_block) = aggregate(
  bad_clicks / total_clicks
  from packedIpAddress range
) → unscaledIpPriorBadFraction
// pre-transformation: linear scaling + offset + min-max
// high bad fraction → click signals from IP degraded
// shared hosting: neighbor spam poisons IP reputation
Affects: Affected by: hosting environment. Shared hosting with spammy neighbors degrades IP reputation. Dedicated IPs or reputable hosting providers avoid this. Sites on compromised IP blocks may see click signals discounted.

## Pipeline
- **Content Analysis**: GibberishScore, keywordStuffing, scamness
- **Link Graph**: spamRank, anchorMismatch, phraseAnchorSpam
- **Domain Signals**: hostAge sandbox, exactMatchDomain, domain trust
- **SpamBrain ML**: Neural network aggregation of all spam signals
- **Action**: Demotion, sandbox, or removal from index

## Timeline
- 2003: Early Spam Filters - Google files first patents on link spam detection and content quality filtering.
- 2011: Anti-Reverse-Engineering Noise (US8612436) - Google patents controlled noise injection into spam scores to prevent SEO reverse engineering. B(x)=K*(x-x^2)^2 peaks near decision boundaries. Enhanced shaping amplifies noise near demotion (0.6) and blocking (0.8) thresholds. Three variation functions for three score ranges. Explains why threshold experiments produce inconsistent results.
- 2012: Penguin Algorithm - Major link spam update. Targets manipulative link building, exact-match anchor text abuse.
- 2015: Real-Time Penguin - Penguin integrated into core algorithm. Link spam penalties now applied in real-time crawl cycles.
- 2018: SpamBrain Launches - Google announces AI-based spam detection system. Uses neural networks for link spam pattern recognition.
- 2023: Yandex Leak Cross-Reference - Yandex's FI_ADV (advertising, weight: -0.251) is the heaviest single negative factor in their entire system. 315 anti-SEO threshold factors detect over-optimization, with 39 operating at the initial retrieval stage (L1), excluding pages BEFORE neural re-ranking. FI_SPAM_KARMA uses Whois data for spam probability. PF filter detects CTR manipulation via IP similarity. FI_COMM_LINKS_SEO_HOSTS (-0.181) penalizes commercial anchor text. This pattern of pre-ranking exclusion suggests Google's Mustang may similarly hard-filter over-optimized pages before they reach the ranking pipeline.
- 2024: API Leak: 26 Spam Signals - phraseAnchorSpamPenalty, spamRank, keywordStuffingScore, anchorMismatchDemotion, and 22 more confirmed.
- 2024: DOJ Trial: Sandbox Confirmed - hostAge signal sandboxes new domains for 3-6 months. GibberishScore detects AI-generated content. scamness scored 0-1023.

## Contradictions
- Public (Google Public): "Domain age is not a ranking factor. A new site has the same chance as an established one."
  Internal (API Leak + DOJ): hostAge signal sandboxes new domains for 3-6 months in serving time. The HostAgeSandbox mechanism explicitly gates new domain visibility.
- Public (Google Public): "We don't penalize sites. We just promote better content."
  Internal (API Leak): exactMatchDomainDemotion, anchorMismatchDemotion, phraseAnchorSpamPenalty are explicit demotion signals applied to specific pages and domains.
- Public (Google Public): "Our spam systems focus on content quality, not links."
  Internal (API Leak): 26 SpamBrain signals with heavy focus on link analysis: phraseAnchorSpamCount, phraseAnchorSpamFraq, spamRank, anchorMismatchDemotion. Link graph analysis remains central.
