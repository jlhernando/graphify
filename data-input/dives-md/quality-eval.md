# Quality Evaluation: IS Score & Human Raters

## Key Insight
The IS Score is Google's north star metric, measured on a 100-point scale by 16,000 human raters worldwide. It directly trains RankBrain, SpamBrain, the Helpful Content System, and MUM. Removing Wikipedia would cause only a ~0.5 IS point loss, quantifying Wikipedia's actual ranking importance. The system evaluates search across 18 quality dimensions including relevance, page quality, popularity, freshness, localization, social fairness, and spam detection.

### Signals,  Core (doj)
- IS Score - Information Satisfaction on 100-point scale (sworn testimony, core)
- IS4@5 - Precision measurement of satisfaction at position 5
- 18SearchQualityAspects - 18 quality dimensions from trial exhibit (core)
- 16,000 human raters worldwide provide training data
- Trains RankBrain, SpamBrain, HCU, and MUM
- Wikipedia removal = ~0.5 IS point loss (quantified)

### Quality Infrastructure (leak)
- Q* - Site-wide quality score (0-1), likely IS-derived
- chardScores - Content quality prediction scores
- racterScores - Content quality evaluator (named after 1984 AI)
- articleScoreV2 - Article quality scoring model
- lowQuality - ML quality classification via site vectors
- Quality infrastructure throughout NSR and CompressedQualitySignals

### 6 Patents (patent)
- Human rater annotations train ML ranking models
- E-E-A-T (Experience, Expertise, Authority, Trust) evaluation criteria
- Side-by-side comparison experiments for algorithm changes
- Quality dimensions include social fairness and privacy
- Localization and language-specific quality assessment
- Spam detection as one of 18 quality evaluation dimensions
- US20100114678: Compensation Distribution Using Quality Score; US9002832B1: Classifying sites as low quality sites; US9098552: Scoring images related to entities
- US7475267: Systems and methods for delay in startup of multiple components; US9183224: Identifying matching canonical documents in response to a visual query; US20090144272: Rating raters

### How Quality Evaluation Affects Your SEO (exploit)
- Study the Quality Rater Guidelines as your scoring rubric: IS Score from 16,000 raters directly trains RankBrain, SpamBrain, HCU, and MUM. The rater guidelines are the closest public document to Google's actual quality criteria. Align your content with E-E-A-T dimensions: demonstrate first-hand Experience, show Expertise with credentials, build Authority through citations and links, and establish Trust with transparent sourcing.
- Target a Q* score above 0.4 for featured snippet eligibility: The leak confirmed Q* is a site-wide quality score on a 0-1 scale, and pages on sites below 0.4 are ineligible for featured snippets. Improve site-wide content quality, not just individual pages. Remove or noindex thin, outdated, or low-quality pages that drag down the aggregate score.
- Optimize across all 18 quality dimensions, not just relevance: DOJ trial exhibits revealed quality evaluation covers relevance, page quality, popularity, freshness, localization, social fairness, and 12 more dimensions. A page can score well on relevance but fail on localization or freshness. Ensure content is geographically appropriate, regularly updated, and accessible.
- Build content that would satisfy a human rater in 30 seconds: Quality raters spend limited time per evaluation. Your page needs to demonstrate expertise and trustworthiness immediately: author bylines with credentials, publication dates, clear sourcing, well-structured content above the fold. If a rater cannot quickly identify why your content is authoritative, the IS Score drops.
- Monitor site-level quality signals via chardScores and lowQuality: The leak revealed ML quality classification via site vectors (lowQuality) and content quality prediction (chardScores). These operate at the site level. One section of consistently poor content, such as auto-generated category pages or thin tag archives, can trigger site-wide quality demotion.
- Benchmark against Wikipedia's quality standard: DOJ testimony revealed removing Wikipedia would cause only a ~0.5 IS point loss, quantifying its importance. Wikipedia succeeds because it scores consistently high across multiple quality dimensions: sourcing, neutrality, comprehensiveness, and regular updates. Model your content structure after Wikipedia's approach for your niche: cite sources inline, maintain neutral tone, update regularly.

### IS Score (DOJ)
100-point satisfaction metric computed from 16,000 human raters worldwide scoring 18 quality dimensions. Raters evaluate live search results against detailed guidelines. Aggregate scores train RankBrain, SpamBrain, HCU, and MUM.
Formula: ISScore(query, serp) = aggregate(
  raterScores[18dims], 16K raters worldwide
)  // 0-100 scale, trains RankBrain/SpamBrain/HCU/MUM
// Wikipedia removal = ~0.5 IS point loss (quantified)
Patents: US8682892 (content quality), US9697259 (quality scoring)
Affects: Affected by: human rater evaluations, 18 quality dimensions, rater geography and guidelines version

### IS4@5 (DOJ)
Precision measurement of information satisfaction at position 5. Evaluates whether the top 5 results collectively satisfy the query intent. Used as a benchmark for algorithm change experiments.
Formula: IS4@5(query) = precision(
  ISScore(results[1..5]), threshold=4
)  // fraction of top-5 results with IS >= 4
// benchmark metric for algorithm change experiments
Affects: Affected by: result diversity, query intent coverage, position 1-5 quality

### 18SearchQualityAspects (DOJ)
18 quality dimensions evaluated by human raters: relevance, page quality, popularity, freshness, localization, social fairness, adult content, web ecosystem impact, spam detection, and more. Each dimension scored independently then aggregated.
Formula: QualityAspects(result) = {
  relevance, pageQuality, popularity, freshness,
  localization, fairness, spam, ... // 18 dimensions total
}.aggregate() → ISScore
Affects: Affected by: content relevance, page quality signals, user engagement, geographic targeting, content safety

### Q* (DOJ+Leak)
Site-wide quality score on a 0-1 scale. 0.4 threshold required for featured snippet eligibility. Likely trained on IS Score data from human raters. Part of QualityNsrPQData in NSR. Aggregated at site level via sitechunks.
Formula: Q*(site) = trainedModel(
  ISScoreData, sitechunks, NSR
)  // 0.0-1.0 scale, QualityNsrPQData
if Q* < 0.4: ineligible(featuredSnippet)
// trained on IS Score human rater data
Patents: US8682892 (content quality), US9697259 (quality scoring)
Affects: Affected by: IS Score training data, site-level content quality, NSR signals, engagement metrics

## Pipeline
- **16K Human Raters**: Worldwide raters evaluate search results quality
- **IS Score**: 100-point Information Satisfaction metric computed
- **18 Dimensions**: Relevance, quality, popularity, freshness, fairness, etc.
- **ML Training**: IS scores train RankBrain, SpamBrain, HCU, MUM
- **Algorithm Eval**: Side-by-side experiments, IS4@5 precision tracking

## Timeline
- 2005: Quality Rater Program - Google establishes human quality rater program. External contractors evaluate search result quality.
- 2013: Quality Rater Guidelines Published - E-A-T framework publicly documented. Expertise, Authoritativeness, Trustworthiness become industry terms.
- 2019: IS Score Formalized - Information Satisfaction metric established as primary quality measurement on 100-point scale.
- 2022: E-E-A-T Update - Experience added to E-A-T framework. Quality dimensions expanded to 18 aspects.
- 2024: DOJ Trial: 16K Raters Revealed - Pandu Nayak testified: IS Score from 16,000 human raters worldwide. Used to train RankBrain, SpamBrain, HCU, MUM.
- 2024: DOJ: 18 Quality Dimensions - Trial exhibit revealed 18 search quality aspects: relevance, page quality, popularity, freshness, and 14 more.

## Contradictions
- Public (Google Public): "Quality raters don't directly influence rankings. They're just for evaluation."
  Internal (DOJ Trial): IS Score from 16,000 raters is used to directly TRAIN RankBrain, SpamBrain, the Helpful Content System, and MUM. Rater judgments are the training signal for every major ranking model.
- Public (Google Public): "Wikipedia has no special treatment in our algorithms."
  Internal (DOJ Trial): Pandu Nayak testified that removing Wikipedia would cause a ~0.5 IS point loss, meaning Google has precisely quantified Wikipedia's contribution. This level of measurement implies deliberate monitoring of Wikipedia's ranking impact.
- Public (Google Public): "We focus on relevance and helpfulness as our primary quality metrics."
  Internal (DOJ Trial): The 18 quality dimensions include "web ecosystem" (Google's market impact), "social fairness" (bias mitigation), "optionalization" (feature A/B testing), and "adult content demotion." Quality evaluation extends far beyond relevance.
