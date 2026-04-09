# Content Quality: From Panda to Helpful Content

## Key Insight
Google uses an LLM to estimate content effort and originality (contentEffort signal). The siteFocusScore measures how dedicated a site is to a specific topic, while siteRadius penalizes pages that deviate from the site's core theme. OriginalContentScore measures uniqueness against the entire web. Together, these enforce topical authority at domain level.

### Panda + Qstar Signals (leak)
- OriginalContentScore - Uniqueness measurement, 0-127 scale
- EffortScore - LLM-estimated effort invested in content
- gibberishScores - AI-generated and low-quality text detection
- Panda modifiers - Site-wide quality adjustment factors (6 signals)
- NSR (44 signals) - Site-level ranking foundation for Q*
- siteAuthority - Domain trust score feeding into Q*

### Quality Signals (doj)
- contentEffort - LLM-based labor and originality estimation
- OriginalContentScore - Content uniqueness vs the broader web
- siteFocusScore - Topical dedication measurement
- siteRadius - Page deviation from site's core theme (higher = worse)
- ugcScore - User-generated content quality assessment
- ugcDiscussionEffortScore - Forum/discussion thread quality

### 2 Patents (patent)
- 2011 Panda - IL/RQ ratio (independent links / reference queries)
- Site-level classifier - Quality applied at domain, not page level
- E-A-T - Expertise, Authoritativeness, Trustworthiness (2018)
- HCU - "Helpful" classifier demotes entire domains (2022)
- Q* threshold - Below 0.4 = ineligible for rich results
- Bill Slawski documented the shift from page-level to site-level quality
- US20090204579: Indexing explicitly-specified quick-link data for web pages; US7047229: Searching content on web pages

### How Content Quality Scoring Affects Your SEO (exploit)
- Maximize contentEffort by showing original work: Google's LLM evaluates labor and originality. Include original research, proprietary data, unique analysis, expert interviews, or custom illustrations. Templated content with swapped keywords scores low. The LLM detects genuine expertise vs. surface-level rewriting.
- Target OriginalContentScore above the duplicate threshold: This 0-127 scale measures content uniqueness against the entire web index. Avoid paraphrasing existing top-ranking pages. Add perspectives, data points, or angles that no other page covers. Syndicated content without canonical tags scores near zero.
- Protect your siteFocusScore by staying in your lane: This signal measures topical dedication via site2Vec embeddings. A cooking site publishing crypto articles dilutes its focus score. If you must expand topics, use subdomains or separate properties to preserve the main domain's topical coherence.
- Monitor siteRadius for every new page: Each page is measured against your site's core semantic centroid. Pages with high siteRadius (far from the site's theme) are flagged as off-topic and can drag down domain quality. Before publishing, ask: does this page fit my site's established topic cluster?
- Audit and remove thin content to protect Q*: Q* is a site-wide quality score, and values below 0.4 disqualify sites from rich results (featured snippets, knowledge panels). Panda modifiers apply at the domain level, meaning a section of low-quality pages can sink the entire site. Prune or noindex thin tag pages, empty category pages, and auto-generated content.
- Treat UGC sections as quality liabilities: ugcScore and ugcDiscussionEffortScore evaluate user-generated content quality. Unmoderated forums, low-effort comment sections, and spammy user reviews actively hurt your Q* score. Implement moderation, minimum quality standards, or noindex low-value UGC pages.
- Avoid gibberishScore triggers with AI content: This signal detects AI-generated and low-quality text. If using AI for content production, ensure heavy human editing, fact-checking, and addition of original insights. Pure AI output, especially at scale, will score poorly on both gibberishScore and contentEffort.

### contentEffort (DOJ: LLM-based labor and originality estimation)
LLM analyzes content to estimate labor and originality. Part of CompressedQualitySignals. Distinguishes genuine expert writing from templated/automated content.
Formula: contentEffort(page) = LLM_eval(
  labor_indicators, originality_markers
)  // in CompressedQualitySignals
// expert writing vs templated/automated detection
Patents: US8682892 (content quality scoring), US9697259 (document quality)
Affects: Affected by: original research, unique data, expert analysis. More human effort and expertise = higher score.

### OriginalContentScore (Leak: 7-bit value 0-127 + DOJ: uniqueness vs broader web)
7-bit value (0-127). Measures content uniqueness vs the broader web. Low scores = scraped or heavily duplicated content.
Formula: OriginalContentScore(page) = dedupe_check(
  content_fingerprint, web_index
)  // 7-bit: 0 (pure copy) to 127 (fully original)
// low values trigger duplicate content filters
Patents: US8682892 (content quality scoring)
Affects: Affected by: publishing original content not found elsewhere. Unique perspectives, data, and analysis score higher.

### siteFocusScore (DOJ: topical authority measurement)
Quantifies how focused a site is on a specific topic. Uses site2Vec embeddings to measure topical coherence. Higher score = more topically focused site.
Formula: siteFocusScore(domain) = coherence(
  site2Vec[all_pages]
)  // higher = more topically focused
// measures embedding variance across all pages
Patents: US9697259 (document quality signals)
Affects: Affected by: staying focused on your niche. Topic-diluted sites score lower. Depth > breadth for topical authority.

### siteRadius (DOJ: page deviation from site core theme)
Measures how far a specific page deviates from the site's core semantic theme. Uses embedding distance. Higher values = more topic drift from site focus.
Formula: siteRadius(page) = distance(
  page_embedding, site2Vec[domain]
)  // higher = more topic drift
if siteRadius > threshold: page flagged as off-topic
// each page measured against site centroid
Patents: US9697259 (document quality signals)
Affects: Affected by: keeping content aligned with your site's core topic. Off-topic pages hurt this metric. Each page should relate to the site's main theme.

## Pipeline
- **Content Analysis**: contentEffort (LLM), OriginalContentScore, gibberishScores
- **Topical Analysis**: siteFocusScore, siteRadius, topic coherence
- **Panda Modifiers**: Site-wide quality adjustments, UGC scoring
- **Q* Score**: 0-1 scale, hand-crafted, query-independent
- **Rich Results Gate**: Q* below 0.4 = no featured snippets, knowledge panels

## Timeline
- 2011: Panda Algorithm - First site-wide quality scoring. Uses IL/RQ ratio (independent links / reference queries) as quality proxy.
- 2014: Panda 4.0 - Major Panda refresh affecting 7.5% of queries. Soft 404 detection and thin content demotion improved.
- 2018: E-A-T Framework - Expertise, Authoritativeness, Trustworthiness formalized in quality rater guidelines. Influences Q* scoring.
- 2022: Helpful Content Update - Site-wide classifier that demotes entire domains producing "unhelpful" content. Domain-level penalty.
- 2023: HCU September Update - Major HCU refresh penalizing AI-generated, low-effort content at scale. Many sites lost 50-90% traffic.
- 2024: API Leak: Content Signals - OriginalContentScore, Panda modifiers, and 19 Qstar signals confirmed. Content quality is multi-layered.
- 2024: DOJ Trial: LLM Effort Scoring - contentEffort uses LLM to estimate labor and originality. siteFocusScore enforces topical authority.

## Contradictions
- Public (Google Public): "We evaluate content on a page-by-page basis."
  Internal (DOJ + Leak): siteFocusScore, siteRadius, and Panda modifiers apply at domain level. One section of low-quality content can drag down an entire site's Q* score.
- Public (Google Public): "There's no penalty for AI-generated content if it's helpful."
  Internal (API Leak + DOJ): GibberishScore actively detects machine-generated text. contentEffort uses LLM to score labor and originality. Low-effort AI content receives measurably lower quality scores.
- Public (Google Public): "Write about whatever topics your audience needs."
  Internal (DOJ Trial): siteFocusScore rewards topical dedication. siteRadius penalizes deviation from core theme. Sites covering too many unrelated topics are measurably disadvantaged in quality scoring.
