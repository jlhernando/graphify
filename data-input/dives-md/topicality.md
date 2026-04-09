# T*: The Topicality Score (ABC Framework)

## Key Insight
HJ Kim confirmed at trial that Google's core relevance scoring follows the ABC framework: Anchors + Body + Clicks = T*. This is the query-dependent signal, meaning it changes for every query. A page about 'Python programming' has a high T* for that query but low T* for 'pet snakes'. T* feeds directly into Mustang's initial retrieval, determining which documents enter the ranking pipeline. Pandu Nayak testified that T*, Q*, and P* are the three pillars of ranking, with T* serving as the relevance foundation.

### DOJ Trial: T* and ABC (doj)
- T*: Query-dependent topicality score. Foundation for initial retrieval. Changes per query
- Anchors (A): What the web says about a document through link context and descriptive anchor text
- Body (B): What the document says about itself through on-page content analysis and term matching
- Clicks (C): What users say about a document through click-through behavior, dwell time, engagement
- titlematchScore: Query-to-title relevance alignment. How well the page title matches the search query
- avgTermWeight: Content emphasis via font sizing analysis. Visual prominence of query terms on page
- T* combines ABC in a hand-crafted way (PXR0356, HJ Kim Feb 2025): T* effectively combines ABC signals (Anchors, Body, Clicks) in a relatively hand-crafted way. ABC signals are the key components of topicality (or base score)
- T* development history (PXR0356): T* was in constant development from origin until ~2020, now less change. 'Ranking development (especially topicality) involves solving many complex mathematical problems.' Dedicated team of engineers working continuously on these problems
- Linear combination of log signals (PXR0357, Pandu Nayak Jan 2025): Top-level signals are a linear combination of log of individual raw signals. Signals formulated such that their impact on ranking is monotonic relative to the signal
- BM25 as baseline (PXR0357): Google's traditional topicality approach was in the style of Okapi BM25. Foundation for body matching before ML enhancements

### Component Signals (leak)
- Anchors sub-signal: Aggregates all inbound anchor text relevance to the query. Source quality tiers apply (HIGH/MEDIUM/LOW)
- Body sub-signal: On-page term frequency, position, prominence. Title tags, headings, body text all contribute
- Clicks sub-signal: NavBoost click data (13-month window). Good clicks vs. bad clicks for the query-document pair
- titlematchScore: Direct title-to-query alignment. Strong title match is a powerful relevance signal
- avgTermWeight: Font size and visual emphasis of terms. Larger/bolder terms weighted more (related to Reasonable Surfer)
- T* is computed at retrieval time in Mustang, before ML re-ranking

### How T* Affects Rankings (exploit)
- ABC alignment is critical: Pages rank best when Anchors, Body, and Clicks all agree on the topic
- Title match is a strong signal: titlematchScore directly measures query-title relevance. Clear, descriptive titles help
- Visual emphasis matters: avgTermWeight means headings, bold text, and font size affect topicality scoring
- Query-dependent: You cannot have a 'good T* score' in general. T* depends entirely on the specific query
- Anchor text must match content: anchorMismatchDemotion fires when anchor topics diverge from page content
- Click signals validate relevance: Pages that attract clicks and engagement for a query get stronger C sub-signal
- Initial retrieval gate: Low T* means a page never enters the ranking pipeline. It's filtered at retrieval

### 19 Patents (patent)
- Google's ranking = T* (Topicality) + Q* (Quality) + P* (Popularity)
- T* is the only query-dependent top-level signal. Q* and P* are mostly query-independent
- T* determines retrieval eligibility. Q* and P* adjust ranking within retrieved results
- Ascorer combines T*, Q*, P* into the initial ranking score before twiddler adjustments
- ML systems (DeepRank, RankEmbed, RankBrain) further refine T* through semantic understanding
- Query stream quality signals - Patent US7962462 (Lamping, Pearson, filed 2005): Derives document quality from query streams. Three detection methods: (1) query text matches document title/URL, (2) anchor text analysis (>50% of matching anchors point to document), (3) user selection patterns (>50% of users click same result). Anti-spam limits: max 50 points per query, 100 per user, 20 per user-document pair, 30 per IP. Foundation for referenceQueries signal
- US20090292683: System and method for automatically ranking lines of text; US7505964: Methods and systems for improving a search ranking using related queries; US5577241: Information retrieval system and method with implementation extensible query architecture
- US7925657: Methods and systems for adjusting a scoring measure based on query breadth; US20100325131: Assigning relevance weights based on temporal dynamics; US8195654: Prediction of human ratings or rankings of information retrieval quality
- US20090171929: Toward optimized query suggeston: user interfaces and algorithms; US6405188: Information retrieval system; US20110035406: User Interface for Presenting Search Results for Multiple Regions of a Visual Query
- US20110022605: Document scoring based on link-based criteria; US8515975: Search entity transition matrix and applications of the transition matrix; US20070088692: Document scoring based on query analysis
- US8868559: Representative document selection for a set of duplicate documents; US20110264671: Document scoring based on document content update; US7421651: Document segmentation based on visual gaps
- US20110043652: Automatically providing content associated with captured information, such as information capture...; US5999924: Method and apparatus for producing sequenced queries; US8051071: Document scoring based on query analysis

### T* (Topicality Star) (DOJ)
T* is computed as a function of three sub-signals: Anchors (A), Body (B), and Clicks (C). The Anchors component measures how relevant the inbound link text is to the query. The Body component measures on-page content relevance through term matching, title alignment, and content structure. The Clicks component uses NavBoost data to measure real user engagement for this query-document pair.
Formula: T*(query, doc) = f(
  A(anchor_relevance),
  B(body_relevance),
  C(click_relevance)
)
// A = what the web says about you
// B = what you say about yourself
// C = what users do on your page
// Query-dependent: changes per query
Affects: Affected by: anchor text relevance, on-page content match, title alignment, user click behavior, term prominence

### Body Sub-Signal (B) (DOJ+Leak)
The Body component of T* evaluates on-page content relevance to the query. It combines term frequency, term position (title vs. heading vs. body), visual prominence (avgTermWeight from font sizing), and title-to-query alignment (titlematchScore). Title match is one of the strongest individual signals within the Body component.
Formula: Body(query, doc) =
  titlematchScore(query, title) *
  termFrequency(query_terms, body) *
  termProminence(avgTermWeight) *
  structuralMatch(headings, query)
// Title match is a strong signal
// avgTermWeight: font size and visual emphasis
Affects: Affected by: title relevance, heading structure, term frequency, font sizing, content depth

### Ranking Combination (T* + Q* + P*) (DOJ)
The three top-level signals combine to produce the initial ranking score. T* provides query-dependent relevance, Q* provides query-independent quality (PageRank, site authority, content quality), and P* provides engagement-based popularity (NavBoost clicks, Chrome visit data). Ascorer is the system that combines these into a single ranking score fed to Mustang.
Formula: Ascorer(query, doc) = combine(
  T*(query, doc),
  Q*(doc),
  P*(doc)
) → initial_rank
// T* = topicality (query-dependent)
// Q* = quality (query-independent)
// P* = popularity (engagement-based)
Affects: Affected by: all T*, Q*, P* component signals. Output feeds predoc twiddlers and ML re-ranking

### Query Stream Quality Signal (Patent US7962462 (Lamping, Pearson, 2005))
Derives document quality signals from search query streams by detecting when users 'ask for' a document by name. Three detection methods: (1) query text matches document title/URL/prominent text, (2) majority (>50%) of anchor text links matching the query point to the document, or (3) majority (>50%) of users clicking on the same result for the query. Assigns quality points with anti-spam limits. Foundation for referenceQueries and navigational quality signals.
Formula: QueryStreamQuality(doc) = sum(
  points(query, doc)
  for query in query_stream
)
// Anti-spam limits:
// max 50 pts/query, 100 pts/user
// max 20 pts/user-doc pair, 30 pts/IP

Detection: titleMatch(q, doc)
  OR anchorMajority(q, doc) > 50%
  OR clickMajority(q, doc) > 50%
Patent: US7962462 (Query stream quality signals, 2005)
Affects: Affected by: branded search volume, user click concentration, anchor text consensus. Documents that users consistently search for by name earn quality points. Foundation for referenceQueries and navigational ranking.

## Pipeline
- **Query Analysis**: Query terms extracted, intent classified, entities identified
- **Anchor Matching (A)**: Inbound anchor text relevance to query computed. Source quality tiers applied
- **Body Matching (B)**: On-page content matched to query: title, headings, body text, term prominence
- **Click Signal (C)**: NavBoost click data (13-month window) for this query-document pair aggregated
- **T* Computation**: ABC sub-signals combined into T* topicality score per document
- **Retrieval Filtering**: Mustang uses T* to select ~10K candidate documents from 400B+ index
- **Score Combination**: T* combined with Q* and P* by Ascorer for initial ranking. Feeds ML re-ranking

## Timeline
- 1998: Term Matching Origins - Original Google combined PageRank with term relevance. Body matching (TF-IDF) is the oldest ranking signal.
- 2005: Query Stream Quality Signals (US7962462) - Google patents quality signal derivation from search query streams. Documents earn points when users 'ask for' them by name via title match, anchor text majority, or click majority. Anti-spam limits cap points per query/user/IP. Foundation for referenceQueries.
- 2015: RankBrain Enhances T* - RankBrain adds ML-based query understanding. Novel queries mapped to known concepts, improving T* for long-tail searches.
- 2019: BERT Deepens Understanding - BERT/DeepRank adds contextual language understanding. T* computation benefits from bidirectional query-document understanding.
- 2020: T* Development Stabilizes (PXR0356) - HJ Kim (Feb 2025 interview) revealed T* was in constant development from origin until ~2020, after which changes slowed. 'Ranking development (especially topicality) involves solving many complex mathematical problems' with a dedicated team of engineers.
- 2025: ABC Framework Confirmed - DOJ trial reveals T* = Anchors + Body + Clicks. HJ Kim confirms T*, Q*, P* as the three top-level ranking pillars. T* combines ABC in a relatively hand-crafted way. Nayak (PXR0357): top-level signals are a linear combination of log of individual raw signals, monotonic, baseline in style of Okapi BM25.
