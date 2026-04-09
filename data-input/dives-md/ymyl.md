# YMYL: Your Money Your Life Quality Gates

## Key Insight
The API leak revealed YMYL is scored as a continuous value, not a binary flag. The ymylNewsV2Score (V2 indicates iterative refinement) and healthScore are computed within the NSR module, meaning YMYL quality assessment is baked into the site-level quality score itself. The DOJ trial confirmed the chard signal as a named YMYL authority component. Combined with the Q* 0.4 threshold (sites below 0.4 are ineligible for featured snippets), YMYL creates a two-layer gate: first, higher quality requirements for YMYL topics, then a hard cutoff for prominent SERP features.

### DOJ Trial Evidence (doj)
- ymylHealthScore: Health and medical content authority assessment. Pages on medical topics must demonstrate expertise and accuracy
- ymylNewsScore: News topic authority and credibility assessment. News content on YMYL topics requires journalistic standards
- chard: Named authority signal specifically for YMYL domains. Covers health, finance, and safety topics
- Q* 0.4 threshold: Sites below 0.4 Q* are ineligible for featured snippets. YMYL content must clear higher Q* to rank prominently
- ConsensusScore: For YMYL health queries, results heavily align with medical consensus. Debunking query classifier triggers consensus-only results

### API Leak: NSR-Embedded YMYL Signals (leak)
- ymylNewsV2Score (QualityNsrNsrData): YMYL score for news content, V2 model. Computed within NSR pipeline, not standalone
- healthScore (QualityNsrNsrData): Health-related content quality assessment. Core signal (isCore: true)
- Both YMYL signals live in the NSR module, meaning they directly affect site-level quality scores
- YMYL classification is a continuous score, not binary. Sites can be partially YMYL-relevant
- NSR aggregates YMYL scores with other quality signals into a single site-level metric

### How YMYL Affects Rankings (exploit)
- Higher quality bar: YMYL topics require demonstrably higher E-E-A-T signals than non-YMYL content
- Medical consensus enforcement: Health queries trigger consensus-aligned results. Anti-consensus health content demoted
- Financial accuracy: Finance content must demonstrate accuracy and authority. Generic financial advice penalized
- News credibility: ymylNewsV2Score evaluates news source credibility on sensitive topics
- Domain authority matters more: chard signal means established YMYL domains have structural advantages
- Featured snippet gating: Q* 0.4 threshold is harder to clear for YMYL content due to higher quality requirements
- Topic classification cascades: A page can trigger YMYL on health, finance, legal, or safety. Each has distinct scoring

### Quality Classification Architecture (patent)
- YMYL classification feeds into NSR (Normalized Site Rank) as component scores
- NSR combines YMYL scores with PageRank_NS, Panda, content quality, and user signals
- NSR feeds into Q* (Quality Star), the top-level quality signal
- Q* combines with T* (Topicality) and P* (Popularity) for final ranking
- YMYL topics are identified through query classification and content classification simultaneously
- Opinion classification for YMYL content - Patent US10832001 (Google, filed 2018): ML model classifies document passages as opinion vs non-opinion (or author opinion vs reported opinion vs non-opinion). Uses RNN/CNN embeddings with lexicon-based features and metadata. Classification scores indicate confidence. Ranks passages by importance and context independence. Clusters documents by opinion patterns for diversity. Critical for YMYL: separates factual medical/financial claims from personal opinions

### YMYL Quality Gate (DOJ+Leak)
YMYL content must pass a higher quality threshold than non-YMYL content. The system computes topic-specific scores (health, news, finance) that feed into NSR. NSR aggregates these into a site-level quality score that feeds Q*. For YMYL topics, the effective quality bar is raised, meaning sites need higher Q* scores to rank in comparable positions.
Formula: YMYLGate(page) =
  classify_topic(content) →
  compute(
    healthScore,
    ymylNewsV2Score,
    chard
  ) → NSR → Q*
// YMYL topics = higher Q* needed to rank
// Q* 
Affects: Affected by: topic classification, domain authority, content accuracy, E-E-A-T signals, consensus alignment

### Health Content Scoring (Leak+DOJ)
Medical and health content receives a dedicated healthScore computed within the NSR module. This score evaluates medical accuracy, source authority (medical institutions, licensed practitioners), and alignment with established medical consensus. The DebunkingQueryClassifier identifies health misinformation queries and forces consensus-only results.
Formula: HealthScore(page) =
  medical_authority(domain) *
  consensus_alignment(content) *
  expertise_signals(author, citations)
// isCore: true (critical signal)
// Debunking queries force consensus-only results
Affects: Affected by: medical source authority, consensus alignment, author credentials, citation quality

### YMYL News Credibility (Leak)
News content on YMYL topics (elections, public health, financial markets) receives a ymylNewsV2Score computed within NSR. The V2 designation indicates this is an iteratively improved model. It evaluates journalistic standards, source reputation, editorial oversight, and factual accuracy on sensitive topics.
Formula: YMYLNewsV2(page) =
  source_reputation(domain) *
  editorial_quality(content) *
  factual_accuracy(claims)
// V2 = iteratively refined model
// Computed within NSR module
Affects: Affected by: source reputation, editorial standards, factual accuracy, topic sensitivity

### Opinion Classification for YMYL (Patent US10832001 (Google, 2018))
ML model classifies document passages as opinion, reported opinion, or non-opinion using RNN/CNN embeddings with lexicon-based features and metadata. Classification scores indicate confidence level. System ranks passages by importance (classification confidence, context independence). Clusters documents by opinion patterns across corpus to ensure diversity. For YMYL topics, this separates factual medical/financial claims from personal opinions, enabling ConsensusScore to evaluate factual accuracy separately from subjective content.
Formula: OpinionScore(passage) = classify(
  embed(passage, RNN/CNN),
  lexicon_features,
  metadata
)
// Output: author_opinion | reported_opinion | non_opinion
// Confidence score per classification

Importance(passage) = f(
  confidence,
  context_independence
)
// YMYL: opinions separated from factual claims
// enables ConsensusScore on factual content only
Patent: US10832001 (Machine learning to identify opinions, 2018)
Affects: Affected by: passage writing style (subjective vs objective language), lexicon features, document metadata. Critical for YMYL: personal health opinions deprioritized vs medical consensus.

## Pipeline
- **Topic Classification**: Query and content classified into YMYL categories: health, finance, legal, safety, news
- **Domain Authority Check**: chard signal evaluates domain-level YMYL authority. Medical sites, financial institutions, government sources scored
- **Content Quality Scoring**: healthScore and ymylNewsV2Score computed per page within NSR module
- **Consensus Evaluation**: For health/science topics, ConsensusScore measures alignment with established knowledge
- **NSR Integration**: YMYL scores integrated into Normalized Site Rank alongside PageRank_NS, Panda, user signals
- **Q* Gating**: Higher effective Q* threshold for YMYL content. Below 0.4 = no featured snippets or rich results

## Timeline
- 2014: YMYL Concept Introduced - Google's Search Quality Evaluator Guidelines first define YMYL as a quality classification for sensitive topics.
- 2018: Medic Update - August 2018 core update heavily impacts YMYL health sites. E-A-T becomes critical for medical content ranking.
- 2018: Opinion Classification Patent (US10832001) - Google patents ML-based opinion vs non-opinion classification for document passages. RNN/CNN embeddings with lexicon features. Multi-class: author opinion, reported opinion, non-opinion. Critical for YMYL: separates personal health/financial opinions from factual claims.
- 2022: E-E-A-T Added - Experience added to E-A-T framework. YMYL evaluation now considers first-hand experience alongside expertise.
- 2024: YMYL Signals Exposed - API leak reveals ymylNewsV2Score and healthScore within NSR module. YMYL is continuous, not binary.
- 2025: chard + Thresholds - DOJ trial reveals chard authority signal for YMYL domains and Q* 0.4 threshold for featured snippet eligibility.
