# Google's Ranking Pipeline: From Crawl to SERP

## Key Insight
Only RankBrain and DeepRank use LLM/ML technologies. All other ranking signals are hand-crafted (HJ Kim deposition). 100+ raw signals are combined via linear weighted formulas into top-level scores. Sigmoid functions and threshold engineering handle signal blending. 16,000 human raters score IS (0-100) which trains RankBrain, SpamBrain, HelpfulContent, and MUM.

### 2 Patents (patent)
- 0-1 scale, query-independent
- Hand-crafted, not ML
- Feeds: NSR, siteAuthority, OriginalContentScore, EffortScore
- Below 0.4: ineligible for rich results
- Represents domain trustworthiness
- US9734211: Personalizing search results; US7783639: Determining quality of linked documents

### User Interaction Signals (leak)
- Combines NavBoost click data with anchor metrics
- goodClicks, badClicks, lastLongestClicks
- Chrome data: visits, scrolls, form submissions
- directFrac: brand/type-in traffic fraction
- 13 months of click data retention

### Query-Document Relevance (doj)
- Three sub-signals: Anchors, Body, Clicks
- A: "What the web says about a document" (anchor text)
- B: "What the document says about itself" (on-page content)
- C: Click-based topicality from user behavior
- QBST memorizes words on relevant pages
- 100+ raw signals with aggregate top-level scores (PXR0357, Nayak Jan 2025): Google uses over 100 raw signals. Top-level signals are aggregates of other raw signals. Combines into a single score determining document rank ('Final IR')
- Debugging interface reveals pipeline (PXR0357): Internal debugging interface shows query expansion/decomposition and table of blue links with scores per top-level signal. Engineers can inspect how each signal contributes to final ranking
- Google avoids predicting clicks (PXR0357): Google avoids simply 'predicting clicks' because clicks are easily manipulated and a poor proxy for user experience. Deliberate design choice to not optimize ranking directly for click prediction

### Ranking Impact and Secrecy (doj)
- Ranking is an 'extreme outlier' in business impact (UPX0219, Eric Lehman May 2020): 'Ranking is an extreme outlier in business impact.' Ranking changes directly affect Google's core revenue
- Search features depend on web results (UPX0219): 'Many search features use web results to understand what a query is about and trigger accordingly.' Web ranking is the foundation for other Google products
- Search technology spreads across Google (UPX0219): 'Technologies developed in search spread out across the company to Ads, YouTube, Play.' Search ranking R&D is Google's core competitive advantage
- Manipulation attempts are well-funded (UPX0204, Eric Lehman Nov 2018): 'Attempts to manipulate search results are continuous, sophisticated, and well-funded.' Internal acknowledgment of the SEO arms race
- Click secrecy policy (UPX0204): 'Do not discuss the use of clicks in search, except on a need-to-know basis. Google has a public position.' Deliberate policy to hide click signal usage from public
- Political bias secrecy (UPX0204): 'Do not discuss political bias in search in writing.' Internal policy to avoid written documentation of political bias considerations

### Mustang (DOJ: Initial retrieval and first-pass scoring)
First-pass scoring system operating at massive scale. Reduces 400B indexed documents to tens of thousands of candidates using lightweight signals (T* topicality, basic quality filters). Mehta opinion (FOF 31, Nayak): 'the GSE must include a retrieval system that narrows the volume of responsive links to tens of thousands, as opposed to millions.' Then 'must decide which results are worth scoring at a more granular level, and then score those hundreds of sites to determine which top 10 or so should be surfaced to the user.' Google classifies queries into more than two dozen 'level-one' classifications (FOF 34).
Formula: Mustang(query) = retrieve(
  Alexandria[400B docs], T*, quality_filters
) → tens of thousands  // Nayak: narrows millions to tens of thousands
→ score(hundreds) → top 10  // then scores hundreds for final 10
// 2 dozen+ level-one query classifications (FOF 34)
Affects: Affected by: basic relevance signals. Title-query match, body content relevance, basic topicality. Must pass this filter to be considered at all. Query classification determines which signals and features apply.

### Twiddlers (DOJ: Late-stage re-ranking filters)
Series of re-ranking functions applied post-Mustang. PredocTwiddlers (hundreds of URLs, fast heuristics for dedup/diversity). LazyTwiddlers (top 20-30 results, full metadata, fine-grained adjustments). Each twiddler is a separate mini-algorithm.
Formula: PredocTwiddlers(candidates) → filter(
  dedup, diversity, DMCA
)  // hundreds of URLs, fast heuristics
LazyTwiddlers(top_results) → refine(
  full_metadata
)  // top 20-30 only, expensive ops
Affects: Affected by: different for each twiddler. OfficialPageTwiddler boosts brand pages. FreshnessTwiddler promotes recent content. YouTubeDensityTwiddler manages video density.

### SuperRoot (DOJ: Query coordination system)
Orchestration layer managing search pipeline flow. Routes queries through QUS > Mustang > Ascorer > Twiddlers > Tangram. Not a ranking signal itself but controls which signals are applied when.
Formula: SuperRoot(query) → route(
  QUS → Mustang → Ascorer → Twiddlers → Tangram
)  // orchestration, not a ranking signal
// controls which signals apply at each stage
Affects: Affected by: not directly actionable. Understanding the pipeline helps optimize for the right stage.

### LocalInterConnectivity (Patent US6725259 (Google, filed 2003))
Re-ranks initial search results based on inter-connectivity within the result set. Documents frequently cited by other high-scoring documents in the result set receive boosted rankings. Removes documents from same/affiliated hosts to prevent manipulation, then calculates LocalScore from the OldScores of linking documents. Final score combines original ranking with local connectivity support. The top k backlinking documents contribute, with a sensitivity parameter m controlling how strongly high-authority cross-references dominate.
Formula: LocalScore(x) = sum(
  OldScore(BackSet(i))m
  for i in top_k
)  // k = top backlinking docs in result set
// m = sensitivity parameter (1-3)
// same-host links excluded (anti-manipulation)

NewScore(x) = (a + LocalScore / MaxLS) ×
  (b + OldScore / MaxOS)
// combines original score with local connectivity
Patent: US6725259 (Ranking search results by reranking based on local inter-connectivity, 2003)
Affects: Affected by: how many other top-ranking pages cite your page, the authority of those citing pages, host diversity of backlinks within the result set. Pages that are 'hubs' cited by multiple result-set documents benefit.

### LinearWeightedFormula (DOJ: 100+ signals combined via linear weights)
100+ raw signals combined into top-level scores using linear weighted formulas. Nayak (PXR0357, Jan 2025): 'Top-level signals are a linear combination of log of individual raw signals.' Signals are log-transformed before linear combination, and 'formulated such that their impact on ranking is monotonic relative to the signal.' HJ Kim (PXR0356, Feb 2025): engineers 'take a sigmoid or other function and figure out the threshold.' For most signals, Google takes relevant data and performs a regression. In the extreme, hand-crafting means manually picking the midpoint. Google's baseline was Okapi BM25.
Formula: Score(doc) = sum(
  wi * sigmoid(log(raw_signal_i))
  for i in 100+ signals
)  // log-transform then sigmoid (Nayak PXR0357)
// monotonicity constraint on all signals
// weights tuned by engineers, NOT learned
// baseline: Okapi BM25 style ranking
Affects: Affected by: performing well across multiple signals. No single signal dominates. Balanced optimization across quality, relevance, and engagement.

## Pipeline
- **GoogleBot**: Crawls web, discovers URLs
- **Multiverse**: Multiple document feeds
- **Alexandria**: Indexing system, 400B docs
- **Mustang**: Retrieval: millions → tens of thousands (Nayak, Mehta FOF 31)
- **Ascorer**: Scores hundreds of candidates: Q* + P* + T* → top 10 (Mehta FOF 31)
- **Predoc Twiddlers**: Process hundreds of URLs
- **Lazy Twiddlers**: Refine top 20-30 results
- **NavBoost**: Early-stage click filter: culls tens of thousands to hundreds (not a final twiddler). Cannot rank docs without click history
- **ML Refinement**: RankEmbed, DeepRank, RankBrain
- **SERP Delivery**: SRP served in 4 chunks: header, body, footer, late footer (UPX2022). Logged-in users +350ms latency. Mobile +50ms server-side

## Timeline
- 2024: Mehta Opinion: Pipeline Architecture Confirmed (Aug 2024) - Judge Mehta's opinion confirmed the full pipeline: retrieval narrows responsive links from millions to 'tens of thousands' (Nayak, FOF 31), then 'score those hundreds of sites to determine which top 10 or so should be surfaced.' Google uses 2 dozen+ 'level-one' query classifications (FOF 34). 78% of users search within only one vertical in short sessions (FOF 34). Only RankBrain and DeepRank use ML; all other signals are hand-crafted. LLMs are 'additional signals that get balanced' against others (Nayak, FOF 102).
- 2017: Google vs Bing: Serving Architecture (UPX2022, DX0099) - Internal analysis: SRP delivered in exactly 4 chunks (header, body, footer, late footer). Bing ~300ms faster server-side. Logged-in queries cost ~350ms extra (Bing: ~100ms). Mobile adds ~50ms server-side. Google SRP payload double Bing's size (~207kb vs ~112kb). Click tracking uses ~50-100 compressed bytes/URL via onmousedown rwt() rewrite. Project Folly launched to close gap. v2-xs model (2024): latency cut from 1628ms to 1370ms while doubling model size (PXR0037).
- 2024: Rankable Gemini Suggestions (PXR0109) - Gemini in Search exec review: 'Provide rankable Gemini suggestions depending on query.' Implies Gemini outputs enter the ranking pipeline as candidates alongside organic results, not bypassing it.
- 2018: Ranking for Research: Secrecy and Scope (UPX0204) - Eric Lehman's internal document: 'Attempts to manipulate search results are continuous, sophisticated, and well-funded.' Policies: 'Do not discuss the use of clicks in search, except on a need-to-know basis' and 'Do not discuss political bias in search in writing.' Lists 18 search quality aspects.
- 2020: Ranking as Extreme Business Outlier (UPX0219) - Eric Lehman (May 2020): 'Ranking is an extreme outlier in business impact.' 'Many search features use web results to understand what a query is about and trigger accordingly.' 'Technologies developed in search spread out across the company to Ads, YouTube, Play.' Ranking is Google's core competitive engine.
- 2025: Pipeline Debugging Interface Revealed (PXR0357) - Nayak interview (Jan 2025): Described internal debugging interface showing query expansion/decomposition and table of blue links with per-signal scores. Over 100 raw signals aggregate into top-level scores. Google deliberately avoids 'predicting clicks' because clicks are easily manipulated and a poor proxy for user experience.

## Contradictions
- Public (Google Public): "Our ranking is powered by advanced AI and machine learning."
  Internal (HJ Kim Deposition): Only RankBrain and DeepRank use ML/LLM. The vast majority of signals are hand-crafted. 100+ raw signals combined via linear weighted formulas.
- Public (Google Public): "We use over 200 ranking factors."
  Internal (DOJ Trial Evidence (Nayak testimony)): Nayak testified there are 'maybe over a hundred' signals, down from historical 200+. Three top-level scores (Q*, P*, T*) dominate. T* has only 3 sub-signals (A, B, C). Most claimed "factors" are sub-components of these three scores. Even Google's own VP contradicts the 200+ marketing claim.
- Public (Google Public): "We don't use clicks to rank search results."
  Internal (DOJ Trial Exhibit UPX0204 (Eric Lehman, Nov 2018)): 'Do not discuss the use of clicks in search, except on a need-to-know basis. Google has a public position.' Internal secrecy policy explicitly designed to maintain the public denial of click usage in ranking.
- Public (Google Public): "Our search results are not politically biased. Algorithms don't have political leanings."
  Internal (DOJ Trial Exhibit UPX0204 (Eric Lehman, Nov 2018)): 'Do not discuss political bias in search in writing.' The policy to avoid written discussion of political bias suggests awareness of bias considerations that contradict the public position of neutrality.
