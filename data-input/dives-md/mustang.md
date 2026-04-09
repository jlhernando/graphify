# Mustang: Core Scoring & Retrieval

## Key Insight
Mustang is the central nervous system of Google Search. It scores documents using OriginalContentScore (0-127, penalizing thin/duplicate content), termWeight (literal word sizing/bolding impact), and pageEmbeddings (neural vectors). The system runs Twiddler functions internally (QualityBoost, RealTimeBoost, WebImageBoost) for post-scoring adjustments. It also detects page types via pageClassification and measures commercialScore for transactional intent.

### Signals Confirmed (leak)
- OriginalContentScore - 7-bit value (0-127) scoring content originality
- termWeight / avgTermWeight - Word size and bolding impact on scoring
- pageEmbeddings - Neural page-level semantic vectors
- STS - Semantic Text Scores, aggregate text understanding
- QualityBoost / RealTimeBoost - Twiddler re-ranking functions within Mustang
- titlematchScore - How well page titles match user queries
- salientTermSet - Extracted salient terms with weights for topical relevance
- rankLabAttachment - Scoring attachment system for ML components like SnippetBrain

### Signal,  Core (doj)
- Mustang - Confirmed as primary retrieval and first-pass scoring system
- Reduces index matches from hundreds of thousands to tens of thousands
- Operates at massive scale before deeper ML processing
- Integrates Twiddler framework for post-scoring adjustments
- Feeds into SuperRoot for query coordination
- NavBoost click data applied after Mustang initial scoring

### 1 Patents (patent)
- Term-based retrieval with BM25-style scoring and term proximity
- Neural embedding retrieval via RankEmbed dual encoders
- Document classification for page type detection (article, product, forum)
- Content richness signals: numImages, bodySize, videoTranscriptQuality
- Page experience: pageSpeedScore, mobileFriendlyScore, interstitials detection
- Snippet scoring via WebChooserScorer for SERP presentation
- Inverted index failover system - Patent US10073874 (Google, filed 2013): Lock-free dual-engine serving with snapshot records for instant failover. Primary engine serves current posting lists; secondary overlays snapshot bytes at recorded offsets. Snapshot records: 128-byte chunks matching CPU cache lines, containing byte offset + original bytes + timestamp + state. Update delay queue (10s) prevents corruption. Recovery window: 5 minutes of snapshots. Ensures index availability during updates

### How Mustang Affects Your SEO (exploit)
- Win at titlematchScore to survive initial retrieval: Mustang reduces 400 billion documents to tens of thousands. titlematchScore measures how well your page title matches the user query. Include your primary keyword phrase naturally in the title tag. Pages that fail this first-pass filter never reach deeper ML scoring stages, regardless of content quality.
- Use HTML formatting to boost termWeight: termWeight literally measures word size and bolding impact on document scoring. avgTermWeight computes the average weighted font size of body terms. Use H1-H6 heading hierarchy with target terms, bold key phrases with strong tags, and structure content so important terms appear in visually prominent positions. This contradicts Google's public claim that formatting does not affect rankings.
- Maximize OriginalContentScore above the 0-127 range: This 7-bit value (0-127) scores content originality within Mustang. Pages with duplicated, heavily paraphrased, or thin content score low and are filtered early. Create genuinely original analysis, proprietary data, unique perspectives, or first-party research. Even comprehensive content that reorganizes existing information without new insight may score low.
- Stay within numTokens max cap to avoid truncation: Documents are truncated at a max token cap in Mustang. Content beyond this cap is not scored. Structure your most important content, key arguments, and primary keyword usage in the first 60-70% of the page. Do not bury critical information at the bottom of extremely long pages.
- Optimize salientTermSet for topical relevance: Mustang extracts salient terms with weights to assess topical relevance. Cover the complete semantic field of your target topic using the terminology experts in that field would use. Tools like TF-IDF analysis of top-ranking competitors can reveal which salient terms you are missing from your content.
- Ensure high textConfidence through clear structure: textConfidence measures how confidently Mustang can assess your text content. Ambiguous, poorly structured, or mixed-topic pages score low confidence. Use clear heading hierarchy, logical content flow, one primary topic per page, and unambiguous language. Clean HTML structure directly improves machine parseability.
- Align with pageClassification for intent matching: Mustang classifies pages by type (article, product, forum, etc.) via pageClassification and measures commercialScore for transactional intent. Ensure your page structure matches Google's expected format for your content type. Product pages should look like product pages; articles should be structured as articles. Misclassification means competing in the wrong category.

### Mustang (DOJ)
Operates at massive scale reducing 400B documents to tens of thousands. Uses lightweight signals: T* topicality (Anchors + Body + Clicks), basic quality filters. Speed-critical stage.
Formula: MustangScore(doc, q) = T*(
Anchors(q), Body(q), Clicks(q)
) + basicQualityFilter  // 400B → ~10K docs
if T* < minThreshold: discard
// speed-critical: lightweight signals only at this stage
Patents: US7580929 (information retrieval), US8180754 (document scoring)
Affects: Affected by: basic relevance must be present. Title-query match, content relevance, fundamental topicality required to pass this filter.

### numTokens (Leak)
Token count in document body. Documents truncated at max cap in Mustang. Part of PerDocData.
Formula: numTokens(doc) = tokenize(
doc.body
).length  // PerDocData, truncated at maxCap
if numTokens > maxCap: truncate(doc)
// content beyond cap not scored by Mustang
Affects: Affected by: very long pages may be truncated. Very short pages lack enough content for proper scoring.

### avgTermWeight (Leak+DOJ)
Average weighted font size of terms in body (Leak). Content emphasis measurement via font sizing analysis (DOJ). Evaluates visual prominence of terms on page.
Formula: avgTermWeight(doc) = mean(
fontSize(term) * boldWeight(term) for term in doc.body
)  // visual prominence of terms
// H1-H6, bold, em tags increase term weight
Affects: Affected by: use headings and emphasized text for important terms. Proper HTML heading hierarchy matters.

### textConfidence (Leak)
Confidence score in text relevance and quality. Part of PerDocData in Mustang system. Measures how confidently Mustang can assess the text content.
Formula: textConfidence(doc) = assess(
textClarity, structureQuality, languageModel
)  // 0.0-1.0, PerDocData
// low confidence = ambiguous or poorly structured content
Affects: Affected by: clear, unambiguous content. Well-structured text that's easy for machines to parse.

## Pipeline
- **Index Lookup**: Term matching, numTokens, bodySize, language classification
- **Content Scoring**: OriginalContentScore, termWeight, STS, titlematchScore
- **Neural Scoring**: pageEmbeddings, rankEmbedInfo, compressedDocEmbedding
- **Quality Signals**: GibberishScore, commercialScore, TagPageScore, focusDocumentScore
- **Twiddlers**: QualityBoost, RealTimeBoost, WebImageBoost, ShoppingScore

## Timeline
- 2010: Caffeine Index - Google launches Caffeine, replacing the old index infrastructure. Mustang becomes the primary serving system.
- 2013: Inverted Index Failover Patent (US10073874) - Google patents lock-free dual-engine index serving with snapshot records. 128-byte cache-line-aligned snapshots enable 5-minute recovery window. 10-second update delay queue prevents corruption. Foundation for index reliability at web scale.
- 2013: Hummingbird Update - Semantic understanding integrated into Mustang. Term matching evolves beyond exact keywords.
- 2015: RankBrain Integration - Machine learning embeddings added to Mustang scoring. Neural signals complement traditional term weights.
- 2019: BERT in Mustang - Deep language understanding model integrated into Mustang retrieval and ranking stages.
- 2024: API Leak: 40 Mustang Signals - OriginalContentScore, termWeight, pageEmbeddings, STS, QualityBoost, RealTimeBoost, and 34 more confirmed.
- 2024: DOJ Trial: Core Retrieval - Mustang confirmed as primary scoring/ranking system reducing index from hundreds of thousands to tens of thousands.

## Contradictions
- Public (Google Public): We use hundreds of signals with no single dominant system.
  Internal (API Leak + DOJ): Mustang is the single system through which every result passes. It handles initial retrieval, scoring, and integrates Twiddler post-ranking. It is the backbone of Google Search.
- Public (Google Public): Word count doesn't matter. Write as much as you need.
  Internal (API Leak): numTokens shows documents are truncated at a max cap in Mustang. bodySize measures raw content length. avgTermWeight computes weighted font size of body terms. Content length directly impacts scoring.
- Public (Google Public): Bold and font size don't affect rankings.
  Internal (API Leak): termWeight is literally described as "word size/bolding impact on document scoring." avgTermWeight computes the average weighted font size of terms. HTML formatting directly influences term importance.
