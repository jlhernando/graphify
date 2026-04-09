# ContentAnalysis: Consensus and Quality Scoring

## Key Insight
The DOJ trial revealed Google's ConsensusScore: a per-page metric that counts passages agreeing with, contradicting, or neutral to web consensus. This means Google actively measures whether your content aligns with established knowledge. The DebunkingQueryClassifier detects queries like 'is the earth flat' and forces consensus-only results. For subjective topics, Google intentionally mixes consensus and non-consensus content. The API leak separately confirmed OriginalContentScore (0-127 scale), EffortScore (LLM-estimated), and gibberishScores as content quality gates.

### DOJ Trial: ConsensusScore (doj)
- ConsensusScore: Counts passages that agree with, contradict, or are neutral to web consensus per page
- Generates a per-page consensus score affecting ranking eligibility per query
- Debunking queries: 'Is earth flat?', 'Do vaccines cause autism?' trigger consensus-only results
- Political/subjective topics: Google intentionally mixes consensus, neutral, and non-consensus content
- DebunkingQueryClassifier: Query classifier detecting debunking intent. Part of broader medical/news/YMYL classification

### API Leak: Content Quality Signals (leak)
- OriginalContentScore (0-127): Measures content originality. Higher scores for unique, non-derivative content
- EffortScore: LLM-estimated content effort level. Distinguishes low-effort from high-effort content production
- gibberishScores: Detects nonsensical, auto-generated, or spam content through language quality analysis
- vlq (Very Low Quality): Boolean flag for content failing minimum quality threshold
- clutterScore: Measures page clutter (ads, popups, distracting elements) relative to content
- racterScores: Content naturalness assessment. Detects machine-generated or templated text patterns

### How Content Quality Affects Rankings (exploit)
- Consensus alignment matters for factual topics: Medical, scientific, and factual content must align with established consensus
- Originality scored 0-127: Derivative content (rewritten articles, aggregation) scores lower. Original research scores higher
- LLM detects content effort: EffortScore uses language models to estimate whether content was carefully produced
- Gibberish detection filters AI slop: gibberishScores catch low-quality AI-generated content
- Page experience impacts quality: clutterScore penalizes pages with excessive ads or poor layout
- Topic sensitivity varies handling: Factual queries enforce consensus. Subjective queries allow diversity

### Quality Signal Architecture (patent)
- ContentAnalysis feeds into NSR and Q* scoring pipelines
- OriginalContentScore, EffortScore, and gibberishScores are pre-computed during indexing (Multiverse phase)
- ConsensusScore is query-dependent: same page can score differently for different queries
- YMYL topics receive amplified ConsensusScore weight: health misinformation penalized more heavily
- Content signals combine with E-E-A-T: Experience, Expertise, Authoritativeness, Trustworthiness
- Gibberish content identification - Patent US8554769 (Thakur, Karanjkar, Levin, Brants, filed 2009): Two-pronged gibberish detection. (1) Language model score: 5-gram LM evaluates probability of word sequences per paragraph, aggregates gibberish ratio (gibberish_terms / total_terms). (2) Query stuffing score: compares frequent terms against query index, Si = Li/index(Ti). Final score = min(LM_score, query_stuffing_score). Actions: remove, demote, or leave unchanged based on thresholds

### ConsensusScore (DOJ)
Google analyzes individual passages in a page's content and classifies each as agreeing with, contradicting, or neutral to the established web consensus on the topic. The per-page ConsensusScore aggregates these passage-level classifications. For factual/scientific queries, high consensus alignment improves ranking. For debunking queries, only consensus-aligned results are shown. For subjective topics, a mix of perspectives is maintained.
Formula: ConsensusScore(page, topic) =
  count(agree_passages) -
  count(contradict_passages) +
  neutral_weight * count(neutral)
// Debunking queries: consensus-only results
// Political queries: intentional perspective mixing
// YMYL health: amplified consensus weight
Affects: Affected by: passage-level topic alignment, query classification (debunking vs. subjective), YMYL category

### Content Quality Composite (Leak)
Multiple content quality signals are computed during indexing and combined into an overall content quality assessment. OriginalContentScore (0-127) measures uniqueness, EffortScore estimates production quality via LLM, gibberishScores detect nonsensical text, and vlq flags very low quality content. These combine to form the content component of the Q* quality score.
Formula: ContentQuality(page) =
  f(
    OriginalContentScore / 127,
    EffortScore,
    1 - gibberishScore,
    1 - clutterScore,
    !vlq
  ) → Q* input
// OriginalContentScore: 0-127 scale
// vlq = true: content below minimum quality
// Pre-computed during Multiverse indexing phase
Affects: Affected by: content originality, production effort, language quality, page layout, content-to-ads ratio

### Gibberish Detection Score (Patent US8554769 (Thakur et al., 2009))
Two-pronged gibberish detection system. Language model score: applies 5-gram language model to each text segment, calculates probability of word sequences. Segments with low probability flagged as gibberish. LM score aggregates gibberish paragraph ratio weighted by per-segment gibberish scores. Query stuffing score: identifies frequently occurring terms, checks if phrases containing them match queries in the search index. Si = Li/index(Ti) where Li = hits and index(Ti) = total queries. Final score = min(LM_score, query_stuffing_score). Threshold determines action: remove from index, demote ranking, or leave unchanged.
Formula: LM_score = f(
  gibberish_terms / total_terms,
  sum(score(gibberish_segments))
)
// 5-gram language model per paragraph

QueryStuffing = f(
  (S1 + S2) / 2,
  max(S1, S2)
)
// Si = Li / index(Ti) per frequent term

GibberishScore = min(
  LM_score, QueryStuffing
)
// min() ensures both signals must agree
Patent: US8554769 (Identifying gibberish content, 2009)
Affects: Affected by: text naturalness (LM probability), keyword stuffing density, query index matching ratio. Foundation for gibberishScores signal in API leak.

## Pipeline
- **Content Extraction**: Alexandria extracts main content, separating it from boilerplate, navigation, ads. Uses DomDistiller-style pipeline: DOM traversal, heuristic scoring (link density, text density, semantic HTML), content clustering. See Content Extraction dive for full algorithm details.
- **Quality Scoring**: OriginalContentScore, EffortScore, gibberishScores, clutterScore computed during indexing
- **Passage Classification**: Individual passages classified as agreeing with, contradicting, or neutral to topic consensus
- **Query Classification**: At query time, DebunkingQueryClassifier determines consensus enforcement level
- **ConsensusScore Computation**: Per-page ConsensusScore computed relative to the specific query's topic and intent
- **Quality Integration**: Content quality signals feed into NSR and Q*. YMYL topics receive amplified quality weighting

## Timeline
- 2011: Panda: First Content Quality - Panda update first systematically evaluates content quality at scale. Site-wide quality scoring begins.
- 2009: Gibberish Detection Patent (US8554769) - Google patents two-pronged gibberish detection: 5-gram language model scoring + query stuffing detection. GibberishScore = min(LM_score, QueryStuffing_score). Foundation for gibberishScores API signal. Thresholds determine remove/demote/pass actions.
- 2018: E-A-T Emphasis - Medic update raises content quality standards. E-A-T becomes critical for YMYL content evaluation.
- 2022: Helpful Content System - HCU uses ML classifiers to detect content created primarily for search engines. Folded into core March 2024.
- 2024: Quality Signals Exposed - API leak reveals OriginalContentScore (0-127), EffortScore (LLM-estimated), gibberishScores, vlq flag.
- 2025: ConsensusScore Revealed - DOJ trial reveals ConsensusScore: passage-level agreement with web consensus. DebunkingQueryClassifier forces consensus for factual queries.
