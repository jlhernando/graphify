# AI Overviews & Generative Search

## Key Insight
FastSearch is NOT traditional Search. DOJ remedies revealed it uses only RankEmbed signals (no link authority, no NavBoost) from a curated index subset. Google admitted "the resulting quality is lower than Search's fully ranked web results." Two RAG approaches exist: generate-then-corroborate vs. retrieve-then-generate. Traditional SEO remains the "entry ticket" to AI Overview citations, as top-3 organic results strongly correlate with cited sources.

### 16 Patents (patent)
- Patent US11769017: Generative summaries for search results - Foundational AI Overviews patent
- Patent US20240289407: Search with stateful chat - AI Mode architecture
- Patent US20240346256: RAG model for response generation
- Patent US20230342411: Short answer generation with multi-source validation
- Patent US20240403564: Privacy-preserving personalized LLMs
- Patent US11663201: Generating query variants using trained generative model - Core query fan-out patent for RAG retrieval
- Patent US11762848: Combining multi-query parameters across shared line of inquiry - Query clustering and fusion
- Patent US10346415: Determining question and answer alternatives - Multi-topic decomposition with search verification
- Patent US8954412: Corroborating facts in electronic documents - Threshold-based fact verification by counting supporting documents
- Patent US10019513: Weighted answer terms for scoring - Term-weight passage scoring for answer extraction
- Patent US10180964: Candidate answer passages - Passage selection criteria for citation eligibility
- US8086600: Interleaving search results; US7827170: Systems and methods for demoting personalized search results based on personal information; US9779139: Context-based filtering of search results
- US8775439: Identifying entities using search results; US7451129: System and method for providing preferred language ordering of search results

### API Leak Signals (leak)
- RankEmbedNearestNeighbors - Embedding-based retrieval used by FastSearch for AI grounding document selection
- SnippetBrainSelectedText - Pre-extracted answer passages from documents, fed into AI Overview generation pipeline
- contentTopicality - Per-document topicality scores determining relevance for AI Overview citation selection
- salientTerms - Key entity and concept terms extracted by Webref, used for query-document alignment in RAG retrieval
- experimentalQstarSignal - Quality gate signals filtering which sources are eligible for AI Overview citation

### DOJ Evidence (doj)
- FastSearch - Separate system from Search for AI grounding (Elizabeth Reid affidavit). Nayak (PXR0357, Jan 2025): FastSearch uses only RankEmbed signals as RAG retrieval for Gemini app
- Uses only RankEmbed signals, not full ranking stack. RankEmbed 'performs poorly for tail queries' (Nayak PXR0357), meaning AI Overviews may have weaker grounding for uncommon searches
- Retrieves fewer documents from curated index subset
- Google admitted lower quality than full Search results
- Prioritizes semantic matching over authority metrics
- Court ordered sharing of GenAI training data in remedies
- SGE/AI Overviews in Mehta opinion (FOF 101, 109): Google developed Search Generative Experience leveraging AI to 'add generative AI into the search results to enhance them' (Elizabeth Reid). Transformers technology 'allowed Google to rely on less user data and still improve its ranking of search results.' But LaMDA/PaLM/PaLM2 'were not built with user data' (FOF 100), while MUM WAS trained on web corpus + click data
- LLM search stack rebuild - Nayak (PXR0357, Jan 2025): Google is 're-thinking their search stack from the ground-up with LLM taking a more prominent role.' Considering how fundamental components (ranking, retrieval, displaying SERP) can be reimagined with LLMs. LLMs can improve query interpretation and summarizing presentation of results. Computation time of LLMs is a key consideration depending on use case. ML training windows shrinking (90→60 days). Suggests fundamental architectural shift beyond adding AI features to existing pipeline
- AI Overviews metrics (PXR0037, Board Update Q2 2024): 61M DAU, 239M MAU. +1.4% inslice en-US SUNs growth, +0.18% overall US SUNs. Multisearch: 99M MAU (3.6x YTD). Latency: 1628ms to 1370ms while doubling model size (v2-xs). 'People who use AI Overviews actually use Search more' (PXR0038)
- Core ranking integrated into AIO (PXR0038): 'AI Overviews use a customized Gemini model, which works in tandem with our existing Search systems, like our quality and ranking systems and the Google Knowledge Graph.' 'We've integrated our core web ranking systems into this experience.' AIO clicks are 'higher quality' with more time spent on sites
- Search grounding architecture (PXR0153, Anthropic Grounding Oct 2024): Vertex translates prompt into Search query, retrieves URLs + snippets (160 chars), model generates grounded response with recitation/corroboration checking. Two-tier quality: Gemini app gets KG, oneboxes, RelQ; Vertex gets web results only; Anthropic gets same as Vertex 'if not less.' Over 1,000 enterprise customers since May 2024
- Publisher controls and grounding monetization (PXR0026, Apr 2024): 6 options for publisher control granularity over SGE. Architecture separates indexing, ranking, display, grounding, training. 'Do not say this opts them out of training, as we don't want to get into the details of distinction between Gemini training and SGE training, and KITE/MUM.' Grounding 'evolving into a space for monetization.' Recommendation: 'silently update, no public announcement'
- NavBoost queries as Gemini pre-training data (PXR0095, Gemini v3-xs 2024): 'Append anonymized NavBoost Queries for selected documents.' Sessions data as distinct pre-training source. YouTube corpus explored. 80B/160B indie publisher tokens filtered from GCC. Search-specific data kept separate from what 'cloud/other customers cannot use'
- Rankable Gemini suggestions (PXR0109, exec review): 'Provide rankable Gemini suggestions depending on query.' Implies LLM outputs treated as ranking candidates alongside organic results. Design principles: 'North star is a product users find valuable,' 'Preserve ads revenue,' 'Preserve a healthy ecosystem'
- No Search cannibalization from Gemini (PXR0116, May 2024): Internal study found 'no statistically significant impact to overall mobile Search usage from adopting the Gemini Chat App.' Non-significant lift in queries/SUNs. Slight query shift from AGA to Chrome via Gemini fallback search
- Three existential threats (PXR0113, Search Entry Points May 2023): (1) New AI access points (chatbots, LLMs), (2) Search intent shifting to mobile apps (>90% user time), (3) iOS 1P search experiences. Strategic response: embed Google Search into third-party apps and AI chatbots. TAM: 180B+ annual queries. Pilot with up to 15 partners
- Search as corpus business (PXR0105, Reid Email May 2024): 'We thought we were in the finding business. Little did we know, we were also in the corpus business.' Search grounding = RAG via Google's index. 'Unprecedented effort across three PAs (Cloud, Core, Search).' Quora built chatbot powered by Search grounding
- Magi: LLM Superpowers for Search (PXR0034, Sep 2023): Framework mapping LLM capabilities to Search: Synthesis (condense info, stitch sources), Creation (generate drafts, enable customization), Reliability (high quality, sources for verification), Breadth (multiformat, rich 1P data, authoritative voices), Conversation (stateful progression, collaboration)

### Optimization Path (exploit)
- Top-3 organic ranking strongly correlates with AI Overview citation
- Semantic alignment with user intent matters more than backlinks for FastSearch
- Comprehensive topic coverage enables extraction by RAG pipeline
- Logical information architecture aids rapid content extraction
- Two RAG modes: generate-then-corroborate, retrieve-then-generate
- Query fan-out: LLM generates synthetic queries for broader retrieval

### Gemini Grounding Pipeline: Live Interception (Apr 2026) (exploit)
- Live interception of Gemini's StreamGenerate endpoint reveals per-citation grounding scores
- Each grounding entry contains: [passage_text, char_offsets], [type, verified_boolean, relevance_score], [[source_url, title, favicon]]
- Relevance score is a 0-1 float measuring semantic similarity between Gemini's generated passage and the source document passage
- Verified boolean indicates whether Google successfully confirmed the cited passage exists in the source at fetch time
- Score distribution from 12 captured citations: range 0.063-0.923, mean 0.668
- Strong correlation: verified=true entries average 0.83 score vs verified=false at 0.39
- Specialist/authoritative sites score highest (motor.mapfre.es=0.923, carwow.co.uk=0.863)
- Low-authority sites get near-zero scores (webuyanycarusa.com=0.063)
- Character offset mapping enables exact source-to-response text highlighting in Gemini UI
- Confirms PXR0153 two-tier grounding architecture: these scores likely determine Tier 1 (Gemini app) vs Tier 2 (Vertex/third-party) citation quality
- FastSearch (RankEmbed-based RAG) confirmed as retrieval mechanism (PXR0357 Nayak), grounding score is the post-retrieval relevance filter
- Main endpoint: /_/BardChatUi/data/assistant.lamda.BardFrontendService/StreamGenerate
- Response format: batchexecute-wrapped streaming JSON with embedded grounding metadata
- CRITICAL: Grounding scores do NOT verify factual accuracy. Source comparison revealed a hallucinated claim ('6 sillas infantiles') scored 0.945 verified=true when the actual source said only '3 ISOFIX'. The score measures SEMANTIC EMBEDDING SIMILARITY (vector distance), not truth. Passages about 'SUV premium con asientos ISOFIX' and 'SUV de gama alta con sillas infantiles' are close in embedding space despite different facts
- Cross-language grounding fails: webuyanycarusa.com scored 0.063 because the source was in English while Gemini's response was in Spanish about entirely different cars. Embedding distance across languages is too large for meaningful grounding
- Same domain can score very differently per passage: coches.net scored 0.945 for one passage and 0.545 for another. Grounding is passage-level, not site-level, unlike Q* which is site-level
- The 'verified' flag correlates with #:~:text URL fragments: verified=true entries typically have text fragment selectors in the source URL, suggesting Google uses Scroll-to-Text to confirm passage existence at the cited location
- Updated dataset: 17 citations captured across 12 unique domains. Score distribution: 0.063-0.946, mean 0.630. Verified=true avg 0.831 vs verified=false avg 0.404

### Query Fan-Out & Source Selection Pipeline (Apr 2026) (exploit)
- Expanded dataset: 32 citations across 22 domains, 9 responses. Score distribution: 0.063-0.956, mean 0.733. High-confidence flag on 75% (24/32). All responses used Gemini 3 Flash model with mode fbb127bbb056c959 (Fast)
- Search queries are 100% server-side. Full browser network interception confirmed ZERO Google Search requests from the client. The entire query fan-out pipeline (reformulation, search, retrieval, grounding) runs server-side within the StreamGenerate endpoint
- Query fan-out patent chain identified: US11663201 (variant query generation via neural net) -> US20240346256 (RAG vector retrieval) -> US20230342411 (multi-source consensus scoring) -> US11769017 (grounded summary with citations). User prompt is decomposed into multiple search-optimized queries server-side
- Google text fragments prove Google Search involvement: 53% of citation URLs contain #:~:text= fragments (Google's Scroll-to-Text feature), confirming Gemini uses Google Search to find and locate specific passages within source pages
- Grounding score is NOT word overlap. Pearson correlation between grounding score and literal word overlap is only 0.199 (weak). High scores (0.9+) can have 0% word match. Confirms pure embedding/semantic similarity, not lexical matching
- Source selection is two-stage: (1) Sites must first rank in Google Search for Gemini's reformulated queries, (2) Then passage-level embedding similarity determines the grounding score. Sites with specific factual data (prices, specs) score higher (PcComponentes 0.951, Back Market 0.956) vs generic overviews (REVEL 0.399, ELLE 0.490)
- Cross-language retrieval: Spanish-language queries pull sources from both .es and .co.uk/.com domains. Embedding similarity works across languages but degrades significantly (webuyanycarusa.com scored 0.063 for English content vs Spanish response)
- Follow-up messages skip search: 4 of 9 captured responses had zero citations. These were conversation follow-ups where Gemini answered from context without triggering new search. Only first messages in a conversation thread reliably trigger grounding
- sourceWeight=50 on all citations suggests a fixed retrieval cutoff (top 50 search results?) or a constant configuration value. sourceType=1 for web search results, sourceType=2 also observed
- New query fan-out patents: US11663201 (generative query variants, 2018), US11762848 (combining multi-query parameters, 2022), US10346415 (multi-topic decomposition, 2016), US9002817 (interleaved multi-engine results), US20090198644 (learned rewrite policies with sequential providers)
- Authority is irrelevant for citation selection. yourcar.es (small Spanish car blog, ranked #7 in organic Google for 'coches maletero grande 2026') was cited by Gemini alongside major sites like Carwow and PcComponentes. Because FastSearch uses only RankEmbed signals (no link authority, no NavBoost, no PageRank per PXR0357), a small topically-focused blog and a major publication are equally eligible. Organic rank position doesn't matter once retrieved; only the grounding score determines citation
- Implication for SEO: To get cited by Gemini, sites need (1) rank in Google Search for likely query reformulations (even position 7+ is sufficient), and (2) have content that is semantically aligned with what Gemini will generate. Topical specificity and factual density matter more than domain authority for AI citations

### 5 Patents (patent)
- Grounding score = Cosine similarity (US20240346256): Patent describes generating feature vectors from query and source content, then computing cosine similarity. Two-stage selection: (1) similarity must exceed predetermined threshold, (2) top-K sources by highest similarity. This maps directly to the 0-1 float scores we intercepted in StreamGenerate responses
- HighConfidence flag = Accuracy threshold (US20230342411): Patent describes accuracy scoring with threshold-based filtering: 'display passage if score > threshold, hide if below.' Trained with sigmoidal cross-entropy loss across multiple thresholds. Maps to the boolean highConfidence field (True when score > ~0.6)
- Fact corroboration = Verified flag (US8954412): Patent creates hypothetical facts, counts supporting documents, accepts fact if support > threshold. May map to the verified boolean and the #:~:text= URL fragments used to confirm passage existence at source
- Citation ordering = Pairwise ranking (US20250124067): LLM performs pairwise comparisons between retrieved sources, generating relative rankings. Determines citation display order
- Adaptive grounding (US11769017): Patent describes tracking user interactions with cited sources and REVISING LLM output based on which documents were accessed. Explains why follow-up queries in a conversation may change citation patterns
- Three scoring layers identified: (1) Embedding similarity for source retrieval (US20240346256), (2) Accuracy/consensus scoring for citation threshold (US20230342411, US8954412), (3) Pairwise ranking for citation ordering (US20250124067). Our interception captures layer 1 (the cosine similarity score) and layer 2 (the highConfidence threshold)

### FastSearch Retrieval (DOJ)
FastSearch retrieves documents for AI Overview grounding using only RankEmbed semantic signals. It searches a curated subset of the full index, prioritizing semantic query-document alignment over traditional authority metrics. This makes topically relevant pages with modest backlinks competitive.
Formula: FastSearch(query) = RankEmbed(
  curated_index,
  semantic_match(query, doc)
)
// NO link authority, NO NavBoost
// Google: "quality is lower than Search"
Patents: US11769017 (generative summaries)
Affects: Affected by: RankEmbed model quality, curated index coverage, semantic alignment

### AI Overview Generation (Patent)
The AI Overview pipeline uses RAG to combine LLM reasoning with retrieved web content. Patent US11769017 describes controls for when to invoke the LLM, how many results to feed it, and text-per-result limits based on latency requirements. Query fan-out generates synthetic queries for broader document retrieval.
Formula: AIOverview(query) = Gemini(
  query,
  FastSearch(query + fan_out(synthetic_queries)),
  latency_budget
) → summary + citations
Patents: US11769017, US20240346256, US20230342411
Affects: Affected by: Gemini model, FastSearch results, latency budget, query complexity

### AI Mode Context (Patent)
AI Mode maintains a persistent context window linking searches across multiple queries. It references prior interactions (clicks, queries, refinements) to dynamically interpret user intent. Patent US20240289407 describes a 'generative companion' that generates synthetic queries using LLMs.
Formula: AIMode(query, context) = Gemini(
  current_query,
  conversation_history,
  FastSearch(rewrite(query, context))
)
// stateful: references prior clicks, queries, refinements
Patents: US20240289407 (stateful chat search)
Affects: Affected by: conversation history, user click patterns, query refinements

### Pairwise LLM Ranking (Patent)
Next-generation ranking patent (2025) using LLM-based pairwise comparison. Instead of scoring documents individually, the model compares pairs of candidate passages and determines relative ranking. Represents a shift from pointwise/listwise to pairwise LLM ranking.
Formula: PairwiseRank(q, d1, d2) = LLM(
  "Which passage better answers: " + q,
  passage_1, passage_2
) → relative_order
// shift from pointwise to pairwise ranking
Patents: US20250124067 (pairwise ranking prompting)
Affects: Affected by: LLM model quality, passage extraction, query understanding

### Query Fan-Out & Grounding (Patent + Interception)
Gemini decomposes the user's natural language prompt into multiple search-optimized queries (fan-out), executes them server-side via Google Search, retrieves source passages, then computes embedding similarity between generated text and source content. The grounding score (0-1) determines citation eligibility. Score has 0.199 correlation with word overlap, confirming pure semantic/vector similarity. 53% of citations use Google text fragments for passage-level source verification.
Formula: FanOut(prompt) = LLM_Decompose(prompt)
  → [query_1, query_2, ... query_n]

Sources = GoogleSearch(queries) → top ~50

GroundingScore(passage, source) =
  CosineSim(Embed(passage), Embed(source))
// Score: 0-1 float, highConf threshold ~0.6
// Pearson(score, word_overlap) = 0.199
// NOT factual accuracy, purely semantic
Patents: US11663201 (query variants), US11762848 (multi-query fusion), US10346415 (multi-topic decomposition)
Affects: Affected by: Query decomposition quality, Google Search ranking for reformulated queries, embedding model, passage extraction quality

## Pipeline
- **Query Classification**: Determine if query warrants AI Overview (not all queries trigger it)
- **Query Fan-Out**: LLM generates synthetic related queries for broader retrieval
- **FastSearch Retrieval**: RankEmbed-only retrieval from curated index subset
- **Content Extraction**: Key passages extracted from retrieved documents
- **Gemini Generation**: LLM synthesizes answer from retrieved content within latency budget
- **Citation Linking**: Generated text linked to source documents for attribution
- **Quality Check**: Output validated against factual consistency checks

## Timeline
- 2023: SGE Launch - Search Generative Experience launches in Labs. Patent US11769017 filed for generative summaries.
- 2023: Short Answer Patents - Patent US20230342411: multi-source answer validation. Consensus-based accuracy checking.
- 2024: AI Overviews Launch - SGE rebranded to AI Overviews. RAG patent US20240346256 filed. Global rollout begins.
- 2024: AI Mode Patent - Patent US20240289407: stateful chat search with persistent context window and synthetic query generation.
- 2023: Three Existential AI Threats Identified (PXR0113) - Google identified three threats (May 2023): AI chatbot access points, search intent shifting to apps (>90% user time), iOS 1P search. Strategy: embed Search into third-party apps and AI chatbots. TAM: 180B+ queries/year.
- 2024: AI Overviews: 61M DAU, Core Ranking Integrated (PXR0037, PXR0038) - Board update Q2 2024: AI Overviews at 61M DAU, 239M MAU. Multisearch: 99M MAU. Latency 1628ms to 1370ms while doubling model (v2-xs). AIO uses 'customized Gemini model in tandem with quality and ranking systems and Knowledge Graph' (PXR0038). AIO clicks are 'higher quality.'
- 2024: Search Grounding: Two-Tier Quality (PXR0153, PXR0105) - Search grounding launched May 2024 on Vertex AI. Gemini app gets full KG/oneboxes/RelQ; Vertex/Anthropic get 'web results only' with lower quality. 160-char snippets. 1,000+ enterprise customers. Reid: 'We were also in the corpus business.' Grounding 'evolving into a space for monetization' (PXR0026).
- 2024: Publisher Controls: Silent Architecture (PXR0026) - 6 options for publisher SGE opt-out. Architecture separates indexing/ranking/display/grounding/training. KITE and MUM named as distinct training systems. Recommendation: 'silently update, no public announcement.' 'Do not say this opts them out of training.' Grounding being monetized.
- 2024: No Search Cannibalization from Gemini (PXR0116) - Internal study (May 2024): 'No statistically significant impact to overall mobile Search usage from adopting the Gemini Chat App.' Non-significant lift in queries. Gemini adoption reinforces search usage, not replaces it.
- 2025: FastSearch Revealed + LLM Stack Rebuild (PXR0357) - DOJ remedies reveal FastSearch as separate system using only RankEmbed for Gemini RAG on Vertex AI and Gemini app for grounding. Lower quality admitted. Nayak: Google 're-thinking search stack from ground-up with LLM.' Fundamental components (ranking, retrieval, SERP display) being reimagined. LLMs improve query interpretation and result summarization, but computation time is key constraint. Training windows shrinking (90→60 days).
- 2025: Pairwise LLM Ranking - Patent US20250124067: LLM-based pairwise comparison ranking. Next-gen approach to neural ranking.
- 2026: Grounding Pipeline Scores Intercepted - Live protobuf interception of Gemini's StreamGenerate endpoint reveals per-citation relevance scores (0-1), verified flags, and character offset mapping. Confirms PXR0153 two-tier grounding architecture. Score range 0.063-0.923 across 12 citations, with verified=true averaging 0.83.
- 2026: Query Fan-Out Pipeline Mapped (32 citations) - Extended interception to 9 responses, 32 citations across 22 domains. Confirmed search queries are 100% server-side (zero Google Search calls from browser). 53% of citation URLs contain Google text fragments (#:~:text=), proving Google Search involvement. Grounding score has only 0.199 correlation with word overlap, confirming pure embedding similarity. Patent chain mapped: US11663201 (query variants) -> US20240346256 (RAG) -> US20230342411 (multi-source scoring) -> US11769017 (grounded citations).

## Contradictions
- Public (Google Public): "AI Overviews are powered by the same high-quality search results you trust."
  Internal (DOJ Trial + PXR0153): FastSearch uses only RankEmbed (no link authority, no NavBoost, no Q*) from a curated index subset. Google admitted the resulting quality is lower. Furthermore, Google deliberately gives Gemini app better search results than Vertex/Anthropic: 'much richer results with KG, oneboxes, and RelQ' vs 'web results only' for third parties.
- Public (Google Public): "Our AI deeply understands your query to provide the best answer."
  Internal (Patent Analysis): The generate-then-corroborate approach means Gemini sometimes generates the answer FIRST and then finds supporting content. The quality depends on FastSearch retrieving relevant documents, which uses a simplified signal set.
- Public (Google Public): "Publishers can control how their content appears in AI features using existing tools like nosnippet."
  Internal (PXR0026 (Publisher Controls, Apr 2024)): Internal recommendation: 'Silently update, no public announcement.' 'Do not say this opts them out of training, as we don't want to get into the details of distinction between Gemini training and SGE training, and KITE/MUM.' Grounding is 'evolving into a space for monetization.' Google deliberately obscures the distinction between display, grounding, and training.
- Public (Google Public): "AI Overviews include citations so you can verify the information and explore further."
  Internal (Rankpedia Research - Gemini Grounding Interception (Apr 2026)): Live interception reveals grounding scores measure semantic embedding similarity, NOT factual accuracy. A hallucinated claim ('6 child seats') scored 0.945 verified=true when the source only mentioned 3 ISOFIX anchors. The embedding vectors for 'SUV premium con asientos ISOFIX' and 'SUV con 6 sillas infantiles' are close in vector space despite containing different facts. Citations create false confidence in unverified claims.
