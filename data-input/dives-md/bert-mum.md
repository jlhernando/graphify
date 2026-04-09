# BERT & MUM: Language Understanding in Search

## Key Insight
The DOJ trial revealed that DeepRank is the internal name for BERT applied to ranking. Pandu Nayak testified that DeepRank signals can be 'decomposed into signals that resembled traditional signals' and that combining both types improved results. Critically, DeepRank only runs on the final 20-30 documents due to computational cost. BERT now processes nearly every English query for understanding, but full neural re-ranking remains limited to top candidates. The API leak confirmed RankEmbed-BERT as a top-level signal alongside Q* and P*.

### 4 Patents (patent)
- Patent US10452978: Attention-based sequence transduction (Transformer / 'Attention Is All You Need') - foundation for BERT, MUM, Gemini
- Patent US10180964: Candidate answer passages - BERT-powered featured snippet selection using query-dependent and query-independent scoring
- Patent US9940367: Scoring candidate answer passages - passage scoring engine for featured snippets
- Patent US10789427: Multi-task multi-modal machine learning - MUM's underlying architecture for processing text+images across 75 languages
- SMITH research (2020): Extends BERT from 512 to 2048 tokens for long document understanding

### DOJ Trial Evidence (doj)
- Pandu Nayak: DeepRank = BERT for ranking. Only runs on final 20-30 docs (too expensive for all candidates)
- DeepRank signals 'decomposable into signals that resembled traditional signals' - combining both improves quality
- RankEmbed-BERT: Enhanced dual encoder model confirmed as top-level ranking signal
- DeepRank 'not only gives significant relevance gains but ties ranking more tightly to language understanding'
- eDeepRank (decomposable variant) allows auditing which traditional signals neural models approximate
- BERT subsumed WebAnswers (UPX0197, Lehman Jan 2019): 'the tidal wave of deep ML that arrived in the last few weeks was a complete shock' for web answers team. 'A deep ML system will clearly outperform Google's 20-year accumulation of relevance algorithms.' BERT reset potentially wiped out past advantages, similar to what happened in machine translation. Risk: 'Google could similarly be beaten in relevance by another company'
- BERT for Sundar (UPX0193): 'Early experiments with BERT applied to Web Ranking suggest very significant improvements in understanding queries, documents and intents.' Competitive moat: 'Any competitor can use BERT. Fortunately, our training data gives us a nice head-start.' Hardware cost: 'needs multiple orders of magnitude more hardware resources (mainly TPUs)'
- Pre-BERT memorization systems (UPX0197, Lehman): Google's ranking relied on massive 'memorization systems' (NavBoost, click-memory) 'much larger than any current ML model' that captured 'seemingly-crucial knowledge about language and the world'
- Artisanal vs end-to-end (UPX0197, Matthew Gray): Google Ads already used end-to-end deep learning by 2019, while Search still used 'artisanal' hand-tuned ranking systems. Concern: 'how to discuss this future without crushing morale'
- MUM computational limits (Nayak testimony): MUM is too computationally expensive for real-time query processing and cannot index comprehensively across the web. Google trains smaller classifier models using MUM's methodology for production deployment. MUM's value is in generating training data and knowledge, not direct real-time inference
- MUM vs RankBERT performance (Mehta FOF 99): RankBERT 'exhibited fairly weak performance' on newer scoring metrics. MUM 'achieved essentially human-level performance.' MUM trained on subset of web corpus + some click training data to 'understand the structure of language and acquire some kind of reasoning abilities' (Lehman/Nayak)
- Generalization systems (Mehta FOF 97): RankBrain, DeepRank, RankEmbed, RankBERT, MUM are 'generalization' systems: 'may not be so good at memorizing facts, but they're really good at understanding language' (Lehman). 'Designed to fill holes in [click] data' by generalizing from data-rich to data-sparse situations
- Older vs newer data requirements (Mehta FOF 98): Older signals (NavBoost, QBST) use up to 1 trillion training examples. Newer ML signals require only 1 billion. But even newer signals 'were designed with user data and continue to be trained on it.' 'Learning from this user feedback is perhaps the central way that web ranking has improved for 15 years' (UPX226)
- LaMDA/PaLM/PaLM2 NOT built with user data (Mehta FOF 100): LaMDA (2021, conversation), PaLM, PaLM2 expanded capabilities but 'were not built with user data' (Nayak). Distinction: search ranking models (MUM, DeepRank) require user data; general LLMs do not
- KITE and MUM as distinct training systems (PXR0026, Publisher Controls Apr 2024): Internal doc reveals 'we don't want to get into the details of distinction between Gemini training and SGE training, and KITE/MUM.' Confirms KITE is a separate training pipeline alongside MUM, both distinct from Gemini's general training
- NavBoost queries improve LLM benchmarks (PXR0095, Gemini v3 2024): 'Anon navboost queries could be huge, given we see MMLU increases from aquarium queries.' NavBoost query data directly improves standard LLM benchmarks (MMLU), bridging ranking data into model quality. Search-specific data enables Search-dedicated Gemini model (v3-xs)

### What BERT Changed for SEO (exploit)
- Stop words now matter: 'to', 'for', 'no', 'not' change query meaning (prepositions, negations)
- Natural language queries understood: 'can you get medicine for someone pharmacy' now works correctly
- Featured snippets improved: BERT selects more relevant passage as answer
- Passage ranking enabled: BERT identifies passage boundaries within long documents (~7% of queries)
- BERT does NOT change how content should be written. It changes how Google understands queries and content
- You cannot 'optimize for BERT'. Write naturally and comprehensively for your topic

### MUM Capabilities & Impact (exploit)
- 1,000x more powerful than BERT (Google's claim). Built on T5 framework
- Multimodal: understands text + images simultaneously. Photo of hiking boots + 'can I hike Mt. Fuji?'
- Multilingual: trained on 75 languages. Surfaces answers from foreign-language sources
- Powers 'Things to Know' SERP feature (topic exploration pathways)
- Powers 'Refine/Broaden This Search' suggestions
- Crisis detection: identifies suicide, abuse, substance queries and shows resources
- Feeds into AI Overviews (combined with PaLM2 and Gemini models)

### Semantic Coherence & AI Content Detection (exploit)
- BERT NLI for contradiction detection: BERT's Natural Language Inference (NLI) architecture classifies text pairs as entailment, contradiction, or neutral. Fine-tuned BERT achieves ~85% F1 on contradiction benchmarks. Uses [CLS] token through an MLP for three-way classification.
- Intra-page coherence scoring: BERT-based models can evaluate whether paragraphs within a single page contradict each other. AI-generated content commonly asserts conflicting facts across sections due to lack of global coherence. This is a detectable quality signal.
- MUM cross-language fact verification: MUM can verify claims against sources in 75 languages, expanding factual validation beyond English-only. A claim on an English page can be checked against authoritative sources in German, Japanese, or Spanish.
- MUM multimodal quality gates: Evaluates whether images/video genuinely support text claims or are decorative filler. Cross-references image relevance to page topic. Generic stock images score lower than original, contextually relevant media.
- Content depth assessment: MUM evaluates whether content provides genuine informational depth vs. surface-level coverage. Can detect the difference between a page that truly explains a topic and one that merely strings together common knowledge without insight.
- BERT 512-token limitation: BERT's context window (512 tokens) limits single-pass coherence analysis. Full-page evaluation likely uses segmented analysis or SMITH (2048 tokens). MUM's larger context enables more holistic page-level assessment.

### DeepRank (BERT for Ranking) (DOJ+Leak)
DeepRank is Google's internal name for BERT applied to document ranking. It uses bidirectional Transformer attention to compute query-document relevance. Due to high computational cost, it only runs on the final 20-30 documents. The model produces a neural relevance score that is combined with traditional signals. The eDeepRank variant decomposes this score into interpretable components matching traditional signal categories.
Formula: DeepRank(query, doc) =
  BERT_crossattn(
    encode(query_tokens),
    encode(doc_tokens[:512])
  ) → neural_relevance_score
// Only top 20-30 docs (too expensive for all)
// eDeepRank decomposes into traditional signal equivalents
Patents: US10452978 (Transformer)
Affects: Affected by: query-document semantic alignment, token overlap, contextual word meaning, document structure

### RankEmbed-BERT (DOJ+Leak)
An enhanced dual encoder model that embeds queries and documents into a shared high-dimensional space. Uses dot product (not cosine) similarity. Trained on 70 days of search logs plus human rater judgments. Confirmed as a top-level ranking signal alongside Q* (quality) and P* (popularity). More efficient than full DeepRank cross-attention, allowing broader application.
Formula: RankEmbedBERT(query, doc) =
  dot_product(
    BERT_encode(query),
    BERT_encode(doc)
  )
// Dot product, NOT cosine similarity
// 70-day training window + human rater data
Affects: Affected by: semantic similarity, embedding quality, training data recency, query-document topic alignment

### BERT Query Understanding (Patent+DOJ)
BERT processes queries bidirectionally, understanding every word in context of all other words simultaneously. This enables comprehension of prepositions ('to usa' vs 'from usa'), negations ('no curb'), and complex intent. Runs on nearly every English query. Does not produce a ranking score directly but improves query interpretation that feeds into all downstream ranking signals.
Formula: BERTUnderstanding(query) =
  bidirectional_encode(all_tokens)
  → intent, entities, relationships
// Nearly every English query since late 2020
// Prepositions + negations now change meaning
Patents: US10452978
Affects: Affected by: query complexity, stop words, prepositions, negations, entity ambiguity

### MUM Multimodal Understanding (Patent)
MUM maps multiple input modalities (text, images) into a unified representation space using the T5 framework. It transfers knowledge across 75 languages and generates language (unlike BERT which only understands). Powers higher-level features like Things to Know, crisis detection, and AI Overviews. Mehta opinion (FOF 99): MUM 'achieved essentially human-level performance' while RankBERT 'exhibited fairly weak performance.' MUM trained on subset of web corpus plus click training data. LLMs are used as 'additional signals that get balanced both against each other as well as against other signals' (Nayak, FOF 102). They did NOT replace NavBoost/QBST.
Formula: MUM(inputs) =
  T5_encode(
    text_modality(query),
    image_modality(photo)
  ) → unified_representation
// 75 languages, 1000x more powerful than BERT
// Generative: can produce language, not just understand
Patents: US10789427
Affects: Affected by: query complexity, multimodal inputs, cross-language content availability, topic depth

### Semantic Coherence Assessment (Research: BERT NLI + DiscoScore)
BERT's NLI capability classifies text pairs as entailment, contradiction, or neutral. Applied to page quality: segment page into paragraph pairs and evaluate pairwise coherence. Content with high contradiction rates between sections signals low editorial quality, a common AI-generated content pattern. DiscoScore (EACL 2023) extends this to discourse-level coherence evaluation using BERT embeddings.
Formula: Coherence(page) =
  avg(
    NLI(para_i, para_j)
    for all adjacent paragraph pairs
  )
// NLI output: entailment (1), neutral (0.5), contradiction (0)
// High contradiction rate = low coherence = quality signal
// Common in AI content: sections assert conflicting facts
Research: DiscoScore (EACL 2023)
Affects: Affected by: logical consistency across page sections, paragraph-level factual alignment, discourse structure quality.

## Pipeline
- **Query Encoding (BERT)**: BERT bidirectionally encodes query tokens. Stop words, prepositions, negations now affect meaning
- **Intent Classification**: BERT-enhanced intent detection: informational, navigational, transactional, YMYL
- **Retrieval (RankEmbed-BERT)**: Dual encoder embeds query + docs into shared space. Dot product similarity for semantic retrieval
- **Passage Identification**: BERT identifies passage boundaries within long documents for section-level scoring
- **DeepRank Re-ranking**: Full cross-attention BERT scores top 20-30 docs. Produces neural relevance score
- **Score Fusion**: Neural scores combined with traditional signals (T*, Q*, P*). Both types improve results together
- **MUM Features**: MUM generates Things to Know, Refine/Broaden, crisis detection, AI Overview content

## Timeline
- 2017: Transformer Patent - US10452978 'Attention Is All You Need' - the foundation for BERT, MUM, and all modern LLMs. Filed by Shazeer, Vaswani et al.
- 2018: BERT Published - BERT paper published. Bidirectional encoding enables understanding words in full context of sentence.
- 2019: BERT Internal Impact (UPX0197, UPX0193) - Grushetsky email thread (Jan 2019): BERT 'abruptly subsumed essentially all preceding work' in WebAnswers. Lehman: 'a deep ML system will clearly outperform Google's 20-year accumulation of relevance algorithms.' BERT bullet points for Sundar: 'our training data gives us a nice head-start' over competitors. Matthew Gray: Search still used 'artisanal' systems while Ads already had end-to-end deep learning.
- 2019: BERT in Search - BERT launched for Google Search (Oct 2019). Affects ~10% of English queries. Largest change to search in 5 years.
- 2020: BERT Everywhere - BERT expanded to nearly every English query. Extended to 70+ languages. SMITH extends to 2048 tokens.
- 2021: MUM Announced - MUM announced at Google I/O. 1,000x BERT. Multimodal (text+images), 75 languages, generative. First use: COVID vaccine search.
- 2023: DOJ Reveals DeepRank - Nayak testifies DeepRank = BERT for ranking. Only runs on top 20-30 docs. Signals decomposable into traditional equivalents.
- 2024: RankEmbed-BERT Confirmed - API leak confirms RankEmbed-BERT as top-level signal alongside Q* and P*. 70-day training window. Dot product similarity.
- 2024: Mehta Opinion: MUM vs RankBERT (Aug 2024) - Judge Mehta's opinion (FOF 99): RankBERT 'exhibited fairly weak performance.' MUM 'achieved essentially human-level performance.' All ML ranking models (RankBrain, DeepRank, RankEmbed, RankBERT, MUM) are 'generalization' systems 'designed to fill holes in [click] data' (FOF 97). Older signals use up to 1 trillion examples; newer require only 1 billion (FOF 98). LaMDA/PaLM/PaLM2 were NOT built with user data (FOF 100). LLMs did NOT replace NavBoost/QBST (FOF 102).

## Contradictions
- Public (Google Public (2019+)): "BERT transforms how Google understands every query. Our AI deeply understands language and content."
  Internal (DOJ Exhibit UPX0197 (Lehman, Jan 2019)): Pre-BERT, Google relied on massive 'memorization systems' (NavBoost click data) 'much larger than any current ML model.' BERT's 'startling conclusion': 'huge amounts of user feedback can be largely replaced by unsupervised learning from raw text.' Google Search still used 'artisanal' hand-tuned systems while Ads already had end-to-end deep learning.
- Public (Google Public): "Our competitive advantage comes from our advanced AI technology."
  Internal (DOJ Exhibit UPX0193 (Bullet Points for Sundar)): 'Any competitor can use BERT or similar technologies. Fortunately, our training data gives us a nice head-start.' The advantage is data, not the model architecture. Google internally acknowledged BERT is open technology; their moat is proprietary training data.
- Public (Google Defense (DOJ Trial)): "Developments in AI and LLMs have reduced the need for user data. Modern GSEs depend less on user data to improve quality and compete."
  Internal (Mehta Liability Opinion (p.232)): Court rejected this: 'Google continues to rely on large volumes of user data at every step of the search journey, and no witness, even from Google, testified that LLMs had sufficiently advanced to supplant user data.' Google still retains 18 months of user data because 'its value outweighs that cost.' ML ranking models (MUM, DeepRank) are 'generalization' systems 'designed to fill holes in [click] data,' not to replace it.
- Public (Google Public (2019)): "BERT is our biggest leap in search in 5 years and represents our continued investment in AI research."
  Internal (DOJ Trial Exhibits UPX0193 + UPX0197 (2019)): Internally, BERT was seen as an existential threat, not just an advance. Lehman warned BERT 'abruptly subsumed essentially all preceding work' and could mean 'Google could similarly be beaten in relevance by another company.' BERT 'needs multiple orders of magnitude more hardware resources (mainly TPUs),' making it a costly defensive move, not a triumphant innovation. Grushetsky worried about team morale if staff learned their 'artisanal' work was being displaced.
