# Patent Timeline: From Foundations to LLM Era

## Key Insight
The pre-2023 era reveals three architectural layers: link-based authority (PageRank 2001, Reasonable Surfer 2010, Distance-Based PageRank 2018), content quality (Phrase-Based Indexing 2009, Panda 2014, Hummingbird 2013), and user signals (Click Feedback 2015, Word2Vec/RankBrain 2017). The DOJ trial confirmed most signals are hand-crafted, not ML. But 2023+ patents show a rapid shift: US20250124067A1 uses LLMs for pairwise ranking, US20260010575A1 fine-tunes LLMs with RL, and US12536233B1 generates AI landing pages. The hand-crafted era is transitioning to a fully LLM-augmented pipeline.

### 1 Patents (patent)
- US7991710: Intrusive feature classification model

### 2023 Foundation Year (patent)

### 2024 LLM Integration (doj)

### 2025 Pairwise & Retrieval (leak)

### 2026 RL & AI Generation (exploit)

### US20250124067A1 Pairwise Ranking (Patent 2025)
LLM-based pairwise document comparison for ranking. Instead of scoring each document independently (pointwise), the model compares two documents at a time to determine which is more relevant. Reduces complexity vs pointwise scoring. Signals shift from individual features to full-document understanding.
Formula: PairwiseRank(d1, d2, q) = LLM(
"Which is more relevant to q: d1 or d2?"
) → {d1 | d2}
// O(n log n) comparisons vs O(n) pointwise
// full-document understanding, not features
Patents: US20200005149 (learning to rank), US10963505 (neural ranking)
Affects: Affected by: LLM model capacity, document pair selection, comparison prompt design, training data quality

### US20260010575A1 RL Fine-tuning (Patent 2026)
Fine-tunes LLMs using search results as supervision signal. Trains reward models via reinforcement learning where search engagement data provides the reward signal. The search engine itself becomes the training ground for the ranking model.
Formula: RL_finetune(LLM) = train(
reward_model(search_engagement),
distill(search_knowledge) → LLM
)  // search engine = training ground
// RL reward from user engagement data
Affects: Affected by: search engagement data, reward model accuracy, RL training stability, search feedback loop quality

### US12536233B1 AI Landing Pages (Patent 2026)
Landing page quality score derived from conversion rates, bounce rates, and CTR. When scores fall below threshold, the system generates AI alternative landing pages. Google may replace publisher pages with AI-generated versions optimized for user satisfaction.
Formula: PageQuality(url) = score(
conversion_rate, bounce_rate, CTR
)
if score < threshold: generate AI_alternative
// Google may replace your page with AI version
Affects: Affected by: conversion rate, bounce rate, CTR, user satisfaction metrics, page quality threshold

### US20250103662A1 PageRank in Transformers (Patent 2025)
Injects PageRank and authority signals directly into transformer attention weights. Bridges classic link-based authority with neural ranking by making the transformer architecture natively aware of graph-based trust signals during inference.
Formula: Attention(Q, K, V) += inject(
PageRank(source), authority(source)
)  // boosts tokens from high-authority sources
// bridges link-based trust + neural ranking
// reduces hallucinations via authority grounding
Patents: US20200005149 (learning to rank), US10963505 (neural ranking)
Affects: Affected by: PageRank scores, transformer attention mechanism, authority signal injection method, model architecture

## Pipeline
- **2001-2022**: PageRank, Panda, Hummingbird, Word2Vec/RankBrain, hand-crafted signals
- **2023**: BERT evaluation, knowledge panels, click refinement
- **2024**: Stateful chat, thematic clustering, distribution-based retrieval
- **2025**: LLM pairwise ranking, 4000x-efficient retrieval, graph reranking
- **2026**: RL fine-tuning from search feedback, AI-generated landing pages

## Contradictions
- Public (DOJ Trial (2024)): "Only RankBrain and DeepRank use ML. All other signals are hand-crafted." (HJ Kim deposition)
  Internal (2025-2026 Patents): US20250124067A1 uses LLMs for pairwise ranking. US20260010575A1 fine-tunes LLMs via RL with search engine feedback. LLMs are replacing hand-crafted signals.
- Public (Classic Search (2005-2022)): Search is a stateless query-response system. Each query is independent.
  Internal (2024 Patent): US20240289407A1 introduces stateful conversational search maintaining context across interactions. Search becomes a dialogue.
- Public (SEO Best Practice): Optimize landing pages manually. Google crawls and indexes what you publish.
  Internal (2026 Patent): US12536233B1 scores landing page quality (conversion, bounce, CTR) and generates AI alternatives when scores are low. Google may replace your page.
- Public (Bill Slawski Era): Click signals feed into NavBoost as aggregated statistics in a "big table."
  Internal (2023 Patent): US11816114B1 introduces sophisticated click weighting: LC|C fractions, query-type adaptation, and presentation bias correction.
