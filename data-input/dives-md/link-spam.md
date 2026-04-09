# Link Spam & Quality: How Google Evaluates Links

## Key Insight
Google tracks anchor spam velocity via phraseAnchorSpamDays (time to discover 80% of spam phrases) and phraseAnchorSpamRate. The Reasonable Surfer model (US7716225) means links in main content, with larger fonts, in relevant context pass more value than sidebar/footer links. spamRank is binary (0 or 65535) and measures your tendency to link TO spam sites, not just receive spam links.

### 10 Signals (leak)
- sourceType - Link source classified as HIGH, MEDIUM, or LOW quality
- trustedAnchors - Anchor text from trusted/authoritative sources
- anchorFontsize - Font size as proxy for link prominence and importance
- freshdocsLink - Link value multiplier favoring newer linking pages
- siteLinkIn / siteLinkOut - Inbound and outbound link counts at site level
- numOffdomainAnchors - Count of unique external anchor texts
- independentLinks - Links from editorially independent sources

### 12 Signals (leak)
- phraseAnchorSpamPenalty - Combined demotion for spammy anchor patterns
- phraseAnchorSpamDays - Days to discover 80% of spam phrases (velocity)
- phraseAnchorSpamFraq - Fraction of anchors matching spam phrase patterns
- phraseAnchorSpamRate - Rate of spam phrase accumulation over time
- spamRank - Binary (0/65535): links to known spammers
- anchorSpamWeight - Weight assigned to spammy anchor patterns
- spamWordScore - Presence of known spam terms in anchor text

### 11 Patents (patent)
- Reasonable Surfer (US7716225): click probability weighting by position, size, context
- Enhanced Reasonable Surfer (US8117209): additional context signals
- Link context evaluation (US9305099): topical relevance of surrounding content
- Link spam detection: spam farm effective mass metric with trust propagation from seed set (US7533092)
- Manipulated article detection: dense bipartite subgraph analysis identifying doorway/target cluster patterns (US7302645)
- Related documents via backlink/forwardlink sets: host-normalized link scoring for discovering spam link patterns (US6754873)
- Anchor text distribution analysis across linking domains
- Link velocity and pattern detection for unnatural building
- Inverse TrustRank: distance FROM known spam sources
- US20090198673: Forum Mining for Suspicious Link Spam Sites Detection; US20100100564: System and method for spam identification; US7908277: Annotating links in a document based on the ranks of documents pointed to by the links
- US8370340: User location reputation system; US7979417: Embedded communication of link information

### How Link Spam Detection Affects Your SEO (exploit)
- Respect the velocity window of phraseAnchorSpamDays: This signal measures how many days it takes to discover 80% of your spam-pattern anchor phrases. Natural link profiles accumulate anchor diversity slowly over months or years. If you acquire 50 links with similar anchor text in 2 weeks, the compressed timeline triggers phraseAnchorSpamRate flags. Pace link acquisition to mirror natural patterns: varied anchors, spread over months, from diverse source types.
- Audit outbound links to avoid spamRank (binary death): spamRank is binary, 0 or 65535, and measures whether you link TO known spam sites. A single value of 65535 means confirmed spam association. Regularly audit your outbound links, especially in user-generated content (comments, forums, directories). Use nofollow on untrusted outbound links. One bad outbound link neighborhood can flip this binary signal.
- Prioritize links that pass Reasonable Surfer weighting: US7716225 weights links by click probability based on position, font size, and context. A link in main content with normal or large font in a relevant paragraph passes far more value than a sidebar blogroll or footer link list. When earning links, prioritize editorial in-content placements over template-based links.
- Diversify sourceType to earn HIGH classification: Google classifies link sources as HIGH, MEDIUM, or LOW quality. Links from editorially independent sources (independentLinks) with high domain authority earn HIGH classification. Guest posts on low-authority blogs, directory submissions, and forum profiles are classified LOW. Focus effort on fewer HIGH-sourceType links rather than volume of LOW-sourceType links.
- Avoid anchor text concentration that triggers phraseAnchorSpamFraq: This signal measures the fraction of your anchors matching spam phrase patterns. If 40% of your backlinks use the exact same commercial anchor text, the fraction triggers demotion regardless of link quality. Maintain anchor diversity: brand name (40-50%), URL (20-30%), generic (15-20%), and exact-match keywords (under 5-10%).
- Monitor anchorMismatchDemotion between link text and page topic: Links with anchor text that does not match the destination page's topic trigger anchorMismatchDemotion. If sites link to your shoe store page with anchor text about "best crypto wallets," this mismatch signals manipulation. Ensure anchor text from linking sites naturally describes what your page is actually about.
- Use trustedAnchors as your quality benchmark: Google tracks trustedAnchors separately, meaning anchor text from authoritative/trusted sources. These anchors carry disproportionate weight in T* topicality scoring. A single anchor from a trusted source (news sites, academic institutions, government sites) can establish topical authority more effectively than hundreds of anchors from untrusted sources.

### phraseAnchorSpamPenalty (Leak)
Combined demotion for spammy anchor text patterns. An explicit penalty signal, not just "ignoring" links as Google publicly claimed post-Penguin 4.0. Aggregates multiple spam indicators (count, rate, fraction) into a single demotion value.
Formula: phraseAnchorSpamPenalty(site) = aggregate(
spamCount, spamRate, spamFraq
) → demotion_value
// explicit penalty, NOT just "ignoring" links
// contradicts post-Penguin 4.0 public claims
Patents: US9002867 (spam detection), US7603350 (trust ranking)
Affects: Affected by: anchor text pattern diversity, spam phrase concentration, link building velocity, source quality

### spamRank (Leak)
Binary signal (0 or 65535). Measures a site's tendency to link TO spam sites, not just receive spam links. Outbound link hygiene directly measured. A single value of 65535 indicates confirmed spam association.
Formula: spamRank(site) = if links_to_spam(
outbound_links, spam_db
): 65535 else 0
// binary: measures OUTBOUND link hygiene
// linking TO spam sites, not receiving spam
Patents: US9002867 (spam detection), US7603350 (trust ranking)
Affects: Affected by: outbound link destinations, linking to known spam domains, link neighborhood quality

### phraseAnchorSpamDays (Leak)
Time (in days) to discover 80% of spam phrases in a site's anchor text profile. Velocity-based detection of unnatural link building. Natural link acquisition accumulates anchor diversity slowly; spam campaigns compress the timeline.
Formula: phraseAnchorSpamDays(site) = days_to_80pct(
spam_phrases(anchor_profile)
)  // velocity detection: natural = slow
if days < threshold: flag unnatural_building
// spam campaigns compress the timeline
Patents: US9002867 (spam detection)
Affects: Affected by: link acquisition speed, anchor text diversity timeline, spam phrase accumulation rate

### sourceType (Leak)
Link sources classified as HIGH, MEDIUM, or LOW quality. Determines how much ranking value each link passes. Classification based on linking domain's authority, trust, and editorial independence. Reasonable Surfer model further adjusts per-link weight.
Formula: sourceType(link) = classify(
domain_authority, trust, editorial_independence
) → {HIGH, MEDIUM, LOW}
link_value *= ReasonableSurfer(position, font, context)
// per-link weight adjusted by click probability
Patents: US7603350 (trust ranking), US9002867 (spam detection)
Affects: Affected by: linking domain authority, editorial independence, domain trust score, link context

### Spam Farm Detection (Patent)
Two complementary approaches to detecting link manipulation networks. (1) Spam farm metric: propagates trust from a curated seed set through the link graph. Pages that receive link equity primarily from untrusted sources (not reachable from seeds) are flagged. The effective mass metric measures how much of a site's link equity comes from spam farm structures. (2) Manipulated article detection: identifies dense bipartite subgraphs where doorway pages (low quality, link-heavy) point to target pages (the beneficiaries). Clustering reveals doorway/target patterns invisible at the individual link level.
Formula: spamFarmScore(page) = effectiveMass(
link_equity - trustPropagation(seed_set)
)  // equity from untrusted sources = spam signal

manipulatedCluster(pages) = detectBipartite(
doorway_set, target_set
)  // dense doorway→target = link scheme
Patents: US7533092 (spam farm detection via trust propagation), US7302645 (cluster-based manipulated article detection)
Affects: Affected by: link graph topology, trust seed distance, bipartite clustering density. Natural link profiles have distributed, diverse link sources with clear trust paths.

## Pipeline
- **Link Discovery**: Crawl finds link, extracts anchor, font, position, context
- **Source Classification**: sourceType: HIGH/MED/LOW based on domain authority and trust
- **Reasonable Surfer**: Click probability: position, font size, relevance, prominence
- **Spam Detection**: phraseAnchorSpam*, spamRank, anchorMismatch, velocity checks
- **T* Anchors**: Clean anchor signals feed into T* topicality score

## Timeline
- 2003: Reasonable Surfer - Google patents US7716225: not all links are equal. Links weighted by click probability based on position, size, font, and context.
- 2005: Spam Farm & Manipulated Article Detection - Patents US7533092 (spam farm effective mass metric via trust propagation from seed set) and US7302645 (cluster-based manipulated article detection using dense bipartite subgraph analysis). Foundation for SpamBrain's link scheme detection.
- 2005: TrustRank Inverse - Inverse seed propagation from known spam sites. Distance from spam seeds measures spam contamination.
- 2012: Penguin Algorithm - Targets exact-match anchor text manipulation, link schemes, and unnatural link patterns. Kills the link building industry.
- 2016: Real-Time Penguin - Penguin integrated into core algorithm. Link spam penalties now per-page and per-link instead of site-wide.
- 2024: API Leak: 22 Link Signals - phraseAnchorSpamPenalty, spamRank, anchorSpamWeight, phraseAnchorSpamDays (velocity), sourceType (HIGH/MED/LOW), trustedAnchors all confirmed.
- 2024: DOJ: Anchors in T* Formula - Anchor text (A) confirmed as sub-signal of T* topicality. "What the web says about a document." Reasonable Surfer model confirmed.

## Contradictions
- Public (Google Public): "We ignore spammy links rather than penalizing for them." (2016, post-Penguin 4.0)
  Internal (API Leak): phraseAnchorSpamPenalty is an explicit demotion signal. anchorSpamWeight actively penalizes. spamRank (binary 0/65535) penalizes for outbound links to spam.
- Public (Google Public): "All links are treated equally. There's no concept of link quality tiers."
  Internal (API Leak): sourceType explicitly classifies sources as HIGH, MEDIUM, or LOW quality. Reasonable Surfer model weights by click probability. anchorFontsize tracks prominence.
- Public (Google Public): "Link building speed doesn't matter. Build links naturally at any pace."
  Internal (API Leak): phraseAnchorSpamDays measures time to discover 80% of spam phrases. phraseAnchorSpamRate tracks accumulation velocity. Rapid link building triggers spam detection.
