# Ascorer: The Primary Ranking Algorithm

## Key Insight
The API leak confirmed Ascorer as a named, standalone ranking module, not just an abstract scoring function. Its description reads: 'primary rankings algorithm before re-ranking adjustments (twiddlers).' This means Ascorer produces the initial ranking order that the entire re-ranking pipeline then modifies. Every twiddler, every ML re-ranker, every lazy adjustment operates on Ascorer's output. It is the foundation score. Without Ascorer, there would be no initial ordering to refine.

### API Leak: Ascorer Module (leak)
- Ascorer: Named module in Content Warehouse API. 'Primary rankings algorithm before re-ranking adjustments'
- Ascorer is the first full scoring pass after Mustang retrieval
- Output feeds directly into Predoc Twiddlers (several hundred URL adjustments)
- Ascorer score is the baseline that all subsequent re-rankers modify
- Tagged as isCore: true, ml-ranking subcategory

### Ranking Architecture Context (doj)
- Ascorer combines the three pillar signals: T* (Topicality), Q* (Quality), P* (Popularity)
- T* provides query-dependent relevance (Anchors + Body + Clicks)
- Q* provides site/page quality (PageRank, Panda, NSR, content quality signals)
- P* provides popularity/engagement (NavBoost clicks, Chrome data, link metrics)
- The combination formula is not publicly known, but all three are confirmed as inputs
- Nayak: Google uses 'maybe over a hundred' signals. Ascorer likely combines many sub-signals beyond T*/Q*/P*

### How Ascorer Affects Rankings (exploit)
- Ascorer determines initial order: All subsequent systems can only adjust, not replace, Ascorer's ranking
- All three pillars must be strong: High T* with low Q* or P* still produces a poor Ascorer score
- Twiddlers can only adjust so much: Predoc and Lazy Twiddlers make refinements, but the foundation matters
- ML re-rankers operate on Ascorer output: DeepRank and RankEmbed score the top 200-300 documents from Ascorer
- No single signal dominates: Nayak confirmed Google uses many signals. No single input to Ascorer can override others
- Position in the pipeline matters: Ascorer runs early. Early mistakes propagate through the entire pipeline

### Pipeline Position (patent)
- Pipeline: Mustang retrieval > Ascorer > Predoc Twiddlers > ML Scoring > NavBoost > Lazy Twiddlers > Tangram
- Ascorer processes ~10K candidate documents from Mustang retrieval
- Output narrowed by Predoc Twiddlers to several hundred
- ML systems (RankBrain, DeepRank, RankEmbed) score top 200-300
- Lazy Twiddlers make final adjustments on top 20-30 results
- Scoring function evaluation framework - Patent US8060497 (Zatsman, Haahr, Cutts, Wu, filed 2009): A/B testing framework for ranking functions. Pair-wise comparisons with diversity-based selection. Score(i) = (2*wins + draws) / (2*trials). Uses probabilistic ranking via cumulative normal distribution. Karma tracking prevents spam evaluations. Authors include Paul Haahr and Matt Cutts
- Adaptive ranking computation - Patent US7028029 (Kamvar, Haveliwala, Golub, filed 2004): Optimizes PageRank iteration by identifying converged nodes and removing them from subsequent passes. Matrix transformation: A = (c(P+D) + (1-c)E)^T. Achieves 50-70% reduction in computation cost. Enables practical PageRank computation at web scale

### Ascorer Composite Score (Leak+DOJ)
Ascorer combines the three top-level signals (T*, Q*, P*) along with additional component signals into a single ranking score per document. The exact combination formula is unknown, but all three pillars are confirmed inputs. The output is a numeric score that determines initial document ordering before any re-ranking.
Formula: Ascorer(query, doc) = combine(
  T*(query, doc),
  Q*(doc),
  P*(doc),
  additional_signals...
) → initial_rank_score
// 'Primary rankings algorithm before re-ranking'
// ~10K docs scored, output feeds entire pipeline
Affects: Affected by: T* (ABC framework), Q* (quality signals), P* (popularity/engagement), all component signals

### Pipeline Score Evolution (DOJ)
A document's score evolves through the pipeline. Ascorer sets the initial score. Predoc Twiddlers make early adjustments (100+ functions). ML systems add neural ranking adjustments. NavBoost re-ranks based on click data. Lazy Twiddlers make final refinements. The final score can differ significantly from Ascorer's initial score, but Ascorer determines which documents enter the pipeline at all.
Formula: score = Ascorer(query, doc)
  → PredocTwiddlers(score)
  → RankBrain(score)
  → DeepRank(score)
  → NavBoost(score)
  → LazyTwiddlers(score)
  → final_position
Affects: Affected by: each pipeline stage can modify the score. Early Ascorer score determines pipeline entry

## Pipeline
- **Mustang Retrieval**: ~10K candidate documents retrieved from 400B+ index based on query matching
- **Ascorer Scoring**: T* + Q* + P* combined into initial ranking score for all ~10K candidates
- **Predoc Twiddlers**: 100+ adjustment functions process several hundred top URLs. Site diversity, freshness, spam filters
- **ML Re-ranking**: RankBrain (all queries), DeepRank (top 200-300), RankEmbed adjust scores using neural models
- **NavBoost Re-ranking**: Click data from 13-month window adjusts rankings based on real user behavior
- **Lazy Twiddlers**: Final adjustments on top 20-30 results. Last chance for quality/diversity enforcement
- **Tangram Assembly**: Final SERP layout with featured snippets, knowledge panels, ads, organic results

## Timeline
- 1998: PageRank + TF-IDF - Original Google scoring: combine link authority (PageRank) with term relevance (TF-IDF). The first 'Ascorer' was simple.
- 2004: Adaptive PageRank Computation (US7028029) - Kamvar, Haveliwala, Golub patent iterative PageRank optimization. Identifies converged nodes and removes them from computation. 50-70% reduction in cost enables web-scale ranking.
- 2009: Scoring Function Framework (US8060497) - Haahr, Cutts, Zatsman, Wu patent A/B testing framework for ranking functions. Pair-wise comparisons scored by (2*wins + draws) / (2*trials). Diversity-based function selection with karma tracking. Infrastructure for continuous ranking improvement.
- 2015: ML Integration - RankBrain added as first ML component. Ascorer now combines traditional signals with neural network scores.
- 2019: DeepRank Layer - BERT/DeepRank added. Ascorer's output now feeds a deeper ML re-ranking stage for top documents.
- 2024: Ascorer Named in API - Content Warehouse API leak confirms Ascorer as named module: 'primary rankings algorithm before re-ranking adjustments.'
- 2025: Three Pillars Confirmed - DOJ trial confirms T*, Q*, P* as inputs. Nayak testifies on pipeline: 'maybe over a hundred' signals combined.
