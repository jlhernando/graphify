# Anchors: The ABC Framework's Link Signal

## Key Insight
The 2024 API leak exposed Google's three-tier anchor quality system: source pages classified as TYPE_HIGH_QUALITY, TYPE_MEDIUM_QUALITY, or TYPE_LOW_QUALITY via the sourceType field. Homepage trust adds another layer: FULLY_TRUSTED, PARTIALLY_TRUSTED, NOT_TRUSTED, NOT_HOMEPAGE. The anchorMismatchDemotion signal penalizes pages whose inbound anchor text doesn't match their content. HJ Kim confirmed at trial that T* = Anchors + Body + Clicks, making anchor text one of three sub-signals in the core topicality score. The phraseAnchorSpam suite (penalty, count, days, rate) feeds into SpamBrain for detecting link manipulation at scale.

### 11 Patents (patent)
- Patent US7260573: Personalizing anchor text scores - Anchor text relevance scored differently per user/context. Not a flat global score
- Patent US8458207: Using anchor text to provide context - Surrounding text of anchor links used as additional context signal beyond the anchor itself
- Patent US7716225: Reasonable Surfer (Dean, Anderson, Battle) - Link value weighted by font size, position, color, context. Directly affects anchor text value
- Patent US7533092: Link-based spam detection - Identifies unnatural link patterns including coordinated anchor text manipulation
- Anchor text has been a core ranking signal since the original Backrub paper (1996). It tells Google what OTHER sites think a page is about
- US8549014: Document scoring based on document content update; US20110238644: Using Anchor Text With Hyperlink Structures for Web Searches; US20090287698: Artificial anchor for a document
- US20090319533: Assigning Human-Understandable Labels to Web Pages; US20090259629: Abbreviation handling in web search; US7089490: Identifying navigation bars and objectionable navigation bars
- US20110033080: Processing techniques for text capture from a rendered document

### API Leak: Anchor Quality Pipeline (leak)
- sourceType (AnchorsAnchor): Source page quality tier: TYPE_HIGH_QUALITY, TYPE_MEDIUM_QUALITY, TYPE_LOW_QUALITY
- homePageInfo (AnchorsAnchorSource): Homepage trust level: FULLY_TRUSTED, PARTIALLY_TRUSTED, NOT_TRUSTED, NOT_HOMEPAGE
- fontsize (AnchorsAnchor): Font size of anchor text used as link prominence signal (Reasonable Surfer)
- droppedLocalAnchorCount: Internal links explicitly excluded from external link calculations
- TrustedAnchors: Quality and trustworthiness assessment of inbound anchor text
- phraseAnchorSpamPenalty: Combined demotion penalty for spammy anchor patterns (feeds SpamBrain)
- phraseAnchorSpamCount: Number of spammy anchor phrases detected pointing to a page
- homepagePagerankNs (AnchorsAnchorSource): Homepage PageRank_NS used as trust anchor for source evaluation

### DOJ Trial: ABC Framework (doj)
- HJ Kim: T* (Topicality) = Anchors + Body + Clicks. Anchor text is one of three foundational sub-signals
- T*: Query-dependent topicality score. Anchors measure what the web says about a document through link context
- anchorMismatchDemotion: Pages penalized when inbound anchor text doesn't match actual page content
- IsAnchorBayesSpam: Bayesian classifier detecting unnatural anchor text patterns indicative of link manipulation
- Three Pillars of Ranking: Body (content), Anchors (web mentions), User-interactions (clicks, attention). Core architectural principle from trial exhibits

### How Anchor Text Affects Rankings (exploit)
- Quality tier matters: A link from a HIGH_QUALITY source passes more value than LOW_QUALITY. Not all backlinks are equal
- Homepage trust propagates: FULLY_TRUSTED homepages transfer more authority than PARTIALLY_TRUSTED or NOT_TRUSTED
- Font size = prominence: Larger anchor text = higher click probability = more value passed (Reasonable Surfer)
- Content-anchor alignment: anchorMismatchDemotion penalizes mismatches. Anchor text should match page topic
- Diversity over volume: IndyRank measures backlink independence. Many unrelated sources > cluster of related sites
- Internal vs external separation: droppedLocalAnchorCount shows Google explicitly separates internal from external link signals
- Spam detection is multi-signal: phraseAnchorSpam tracks penalty, count, days, and rate. Patterns detected over time, not just volume
- Context beyond the link: US8458207 patents using surrounding text as context. The paragraph around your link matters
- Yandex cross-reference: BM25 and per-query link relevance: Yandex uses BM25 (not TF*IDF) for both anchor text and body text relevance, validating the methodology behind Google's T* scoring. Yandex's FI_LINK_RELEV evaluates link relevance on a per-query basis, making link value dynamic rather than static. This confirms that the same backlink can have different ranking value depending on the search query. Yandex also tracks 146 distinct anchor text factors with different weights for exact match, phrase match, and query term dispersion across links

### T* Topicality Score (Anchor Component) (DOJ)
T* is the query-dependent topicality score computed during initial retrieval. It combines three sub-signals: Anchors (what other sites say about the page through link text), Body (the page's own content), and Clicks (user interaction data from NavBoost). The Anchors sub-signal evaluates all inbound anchor text, weighted by source quality tier and homepage trust level.
Formula: T*(query, doc) = f(
  Anchors(query, doc),
  Body(query, doc),
  Clicks(query, doc)
)
// Anchors = what the web says about this page
// Body = what the page says about itself
// Clicks = what users do on this page
Affects: Affected by: anchor text relevance, source quality tier, homepage trust, body content match, NavBoost click data

### Anchor Source Quality Scoring (Leak)
Each inbound link is scored based on the source page's quality tier (HIGH/MEDIUM/LOW) and the source domain's homepage trust level (FULLY_TRUSTED/PARTIALLY_TRUSTED/NOT_TRUSTED/NOT_HOMEPAGE). Links from high-quality, fully-trusted sources pass significantly more anchor value. Font size of the anchor text adds a prominence multiplier (Reasonable Surfer model).
Formula: AnchorValue(link) =
  weight(sourceType) *
  weight(homePageInfo) *
  prominence(fontsize, position)
// sourceType: HIGH > MEDIUM > LOW
// homePageInfo: FULLY_TRUSTED > PARTIALLY > NOT
// Reasonable Surfer: font size, position matter
Patents: US7260573, US7716225
Affects: Affected by: source page quality tier, homepage trust level, font size, link position, anchor text context

### Anchor Mismatch Demotion (DOJ+Leak)
When the aggregate inbound anchor text pointing to a page doesn't match the page's actual content, Google applies a demotion. This catches pages that have manipulated their backlink profile with irrelevant anchor text, or pages that have changed content but still carry old anchor text signals. The Bayesian classifier (IsAnchorBayesSpam) detects unnatural patterns.
Formula: AnchorDemotion(doc) =
  mismatch(
    aggregate_anchors(doc),
    content_topic(doc)
  ) + BayesSpam(anchor_patterns)
// anchorMismatchDemotion: topic mismatch penalty
// IsAnchorBayesSpam: unnatural pattern detection
// phraseAnchorSpam: rate + count + days tracking
Patents: US7533092
Affects: Affected by: anchor-content topic alignment, anchor text diversity, spam pattern detection, manipulation timeline

## Pipeline
- **Link Discovery & Crawl**: Trawler discovers links during crawl. Alexandria extracts anchor text, surrounding context, font size, position, and link attributes
- **Source Quality Classification**: Each source page classified into quality tiers (HIGH/MEDIUM/LOW). Homepage trust assessed (FULLY_TRUSTED to NOT_TRUSTED)
- **Anchor Text Extraction**: Anchor text parsed with surrounding context (US8458207). Internal links separated from external (droppedLocalAnchorCount)
- **Reasonable Surfer Weighting**: Each link weighted by click probability: font size, position, color, context, topical relevance (US7716225)
- **Spam Detection**: phraseAnchorSpam suite evaluates patterns: penalty score, spam count, days active, manipulation rate. IsAnchorBayesSpam classifier runs
- **Mismatch Check**: anchorMismatchDemotion compares aggregate anchor text topics against actual page content. Mismatches demoted
- **T* Computation**: Anchor scores combined with Body and Clicks sub-signals to compute T* (Topicality). Fed into Mustang for initial retrieval ranking

## Timeline
- 1996: Backrub Paper - Brin and Page's original paper identifies anchor text as a key signal: 'what others say about you matters more than what you say about yourself.'
- 2004: Reasonable Surfer - US7716225 filed. Not all links are equal. Font size, position, color, context determine how much anchor value passes.
- 2006: Personalized Anchor Scores - US7260573: Anchor text relevance scored differently per user/context. Anchor scores are not global constants.
- 2009: Anchor Context Patent - US8458207: Surrounding text of anchor links used as additional signal. The paragraph around a link provides context.
- 2012: Penguin Update - Google targets manipulative anchor text. Over-optimized exact-match anchors penalized. Natural anchor diversity rewarded.
- 2023: Yandex Leak Cross-Reference - Yandex uses BM25 (not TF*IDF) for anchor text and body text relevance scoring, validating Google's T* methodology. 146 anchor-specific factors (TG_LINK_TEXT) with different weights for exact match vs phrase match vs query alignment. FI_LINK_RELEV evaluates link relevance dynamically per-query. Link age is an explicit factor (older links weighted more). Non-commercial link ratio tracked separately (FI_COMM_LINKS_SEO_HOSTS, weight: -0.181).
- 2024: Quality Tiers Revealed - API leak exposes three-tier source quality (HIGH/MEDIUM/LOW), homepage trust levels, phraseAnchorSpam suite, fontsize signal.
- 2025: ABC Framework Confirmed - HJ Kim confirms T* = Anchors + Body + Clicks. Anchor text is one of three foundational pillars of Google's topicality scoring.
