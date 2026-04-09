# Product Reviews: Quality Tiers in Review Content

## Key Insight
Google classifies reviews into explicit quality tiers. productReviewPUhqPage marks individual pages as Ultra High Quality, while productReviewPUhqSite applies the UHQ label at domain level, creating a powerful site-wide signal. Demotion operates at both page level (productReviewPDemotePage) and site level (productReviewPDemoteSite), confirming that one bad review section can tank an entire domain.

### Review Signals (leak)
- productReviewPPromotePage - Page-level promotion for quality reviews
- productReviewPDemoteSite - Site-level demotion confidence score
- productReviewPUhqPage - Ultra High Quality page indicator
- productReviewPUhqSite - UHQ at site level (strongest review signal)
- productReviewPReviewPage - Review page classification flag

### Signals Confirmed (doj)
- productReviewPPromotePage - High-quality review page promotion
- productReviewPDemotePage - Poor quality review page demotion
- productReviewPUhqPage - Ultra High Quality classification for exceptional reviews
- Page-level AND site-level signals operate independently

### 4 Patents (patent)
- First-hand experience with the product
- Original research, testing data, and quantitative measurements
- Expert authorship with demonstrated expertise
- Comparison with alternatives (not just one product)
- Pros AND cons (not purely promotional)
- Links to purchase from multiple merchants
- Domain-specific sentiment classification - Patent US20090125371 (Google, filed 2007): Classifies review sentiment using Maximum Entropy model with domain-specific lexicons. Base lexicon of ~360 sentiment words expanded via WordNet synonyms. Domain-specific terms extracted from high-sentiment documents in each product category. N-gram features up to n=5 capture phrase-level sentiment (e.g., 'not worth the price'). Enables per-aspect sentiment scoring for review quality assessment
- US20110125736: Selecting High Quality Reviews for Display; US7302674: Automating document reviews in a project management system; US7870131: Mitigation of search engine hijacking

### How Product Reviews Affects Your SEO (exploit)
- Target UHQ classification with original testing data: The leak confirmed productReviewPUhqPage as an explicit 'Ultra High Quality' tier, the highest review classification. To earn UHQ, include original benchmarks (speed tests, durability tests, measurements), unique photography showing the product in use, long-term usage observations (not just unboxing), and quantitative comparisons against specific alternatives.
- Treat review quality as a site-level concern, not per-page: productReviewPDemoteSite operates at the domain level with a confidence score. One section of thin affiliate reviews can trigger site-wide demotion that drags down even your best review content. Audit all review pages and either substantially improve or noindex/remove thin reviews that just restate manufacturer specs.
- Demonstrate first-hand experience with specific evidence: The patent pillar confirms Google evaluates first-hand product experience. Include details only a real user would know: setup quirks, day-30 vs day-1 performance, wear patterns, software update impacts. Generic reviews that could be written from a product listing page will not reach the Promote threshold.
- Include genuine pros AND cons to avoid demotion: productReviewPDemotePage flags purely promotional content. Reviews that only praise a product trigger the 'affiliate-only' classification. Include meaningful criticisms, limitations, and specific use cases where the product falls short. This signals editorial independence to the review quality classifier.
- Build independent links to strengthen the IL/RQ ratio: The leak confirmed independentLinks counts unique linking root domains, used in the Panda modifier (IL/RQ ratio). Review sites need links from diverse, independent domains, not just the merchants they review. Earn citations from forums, social discussions, and other publications that reference your review methodology.
- Compare multiple products within each review: Patent evidence confirms comparison with alternatives is a UHQ signal. Do not review products in isolation. Every review should position the product against 2-3 specific competitors with direct, experience-based comparisons on concrete criteria (price, performance, durability). This also increases asteroidBeltIntents coverage for comparative queries.
- Link to purchase options from multiple merchants: Google's quality criteria include multi-merchant purchase links as a UHQ indicator. Providing options from Amazon, direct brand stores, and other retailers signals editorial independence rather than single-affiliate dependence. This also reduces the commercial score skew that flags affiliate-only content.

### productReviewPPromotePage (DOJ: page-level promotion for high-quality reviews)
Page-level promotion signal in PerDocData module. Identifies and boosts high-quality product review content. Likely uses LLM-based analysis of review depth and originality.
Formula: reviewPPromote(page) = LLM_eval(
depth, originality, first_hand_evidence
)  // page-level, in PerDocData module
if quality > promote_threshold: boost ranking
Affects: Affected by: writing in-depth reviews with hands-on testing evidence. Include original photos, comparisons, pros/cons. Demonstrate first-hand experience.

### productReviewPDemotePage (DOJ: page-level demotion for poor quality reviews)
Inverse of promotion signal. Page-level demotion for thin, affiliate-only, or templated product review content. Identifies low-effort reviews.
Formula: reviewPDemote(page) = detect(
thin_content, affiliate_only, templated
)  // inverse of promotion signal
if low_effort == true: demote ranking
Affects: Affected by: avoiding thin reviews that just list specs. Don't rely on manufacturer descriptions. Original testing and analysis prevents demotion.

### productReviewPUhqPage (DOJ: Ultra High Quality classification)
Top-tier quality classification. "Ultra High Quality" designation for exceptional product review pages. Likely reserved for reviews with extensive original testing, unique data, expert analysis.
Formula: reviewPUhq(page) = classify(
testing_depth, unique_data, expert_analysis
) → UHQ tier  // highest quality classification
// reserved for exceptional reviews with original research
Affects: Affected by: aiming for exceptional depth: original benchmarks, long-term testing, expert perspective, comparison methodology. This is the highest tier of review quality.

### independentLinks (Leak: count of linking root domains, Panda modifier IL/RQ ratio)
Count of independent linking root domains. Used as part of the Panda modifier factor (IL/RQ ratio = Independent Links / something). Higher ratio = more natural link profile.
Formula: IL_RQ(site) = independentLinks(unique_root_domains)
/ referenceQueries(branded_searches)
// Panda modifier: higher ratio = more natural profile
// counts linking root domains, not total links
Patents: US9165040 (link graph distances), US7603350 (trust-based ranking)
Affects: Affected by: earning links from diverse, independent domains. Links from many unique domains signal that content is genuinely useful and referenced widely.

### Domain-Specific Sentiment Classification (Patent US20090125371 (Google, 2007))
Classifies review sentiment using Maximum Entropy (MaxEnt) model with domain-specific lexicons. Base lexicon of ~360 sentiment words (positive and negative) expanded via WordNet synonym sets. Domain-specific sentiment terms extracted automatically from documents with highest/lowest sentiment scores within each product category. N-gram features up to n=5 capture phrase-level sentiment patterns (e.g., 'not worth the price' as a negative 5-gram). Per-aspect scoring enables granular review quality assessment: a review can be positive on build quality but negative on battery life.
Formula: Sentiment(review) = MaxEnt(
  base_lexicon(~360 words),
  WordNet_expand(synonyms),
  domain_lexicon(category),
  ngrams(n=1..5)
)
// domain-specific: terms vary by product category
// per-aspect: sentiment scored per product dimension
// phrase-level: n-grams capture negation patterns
Patent: US20090125371 (Domain-specific sentiment classification, 2007)
Affects: Affected by: review vocabulary richness, use of domain-specific terms, balanced sentiment expression, per-aspect detail. Reviews with nuanced, category-specific sentiment score higher.

## Pipeline
- **Page Detection**: productReviewPReviewPage classification
- **Quality Assessment**: Experience, research, expertise analysis
- **Tier Assignment**: UHQ / Promote / Neutral / Demote
- **Site Aggregation**: Page signals roll up to site-level scores
- **Ranking Impact**: UHQ sites dominate review SERPs

## Timeline
- 2007: Domain-Specific Sentiment Patent (US20090125371) - Google patents review sentiment classification using Maximum Entropy model with domain-specific lexicons. Base lexicon of ~360 sentiment words expanded via WordNet. Domain-specific terms extracted per product category. N-gram features up to n=5 capture phrase-level sentiment patterns.
- 2018: Review Snippet Enhancements - Google expands review rich results. Review quality becomes a factor in snippet eligibility.
- 2021: Product Reviews Update v1 - Google targets low-quality affiliate reviews. Sites with thin comparison content see significant traffic drops.
- 2022: Product Reviews Updates v2-v4 - Three more updates in 2022 alone. Focus shifts to first-hand experience, original research, and quantitative data.
- 2023: Reviews System Integration - Product reviews system merged into core algorithm. Runs continuously rather than as periodic updates.
- 2024: API Leak: Review Signals - productReviewPPromotePage, productReviewPDemoteSite, productReviewPUhqPage, productReviewPUhqSite confirmed.
- 2024: DOJ Trial: UHQ Classification - Ultra High Quality review tier confirmed. Page-level promotion/demotion and site-level signals revealed.

## Contradictions
- Public (Google Public): We evaluate review quality holistically. There's no single quality threshold.
  Internal (API Leak): Explicit binary and tiered signals: productReviewPUhqPage is a distinct "Ultra High Quality" classification. productReviewPDemoteSite is a site-level demotion with a confidence score.
- Public (Google Public): Product reviews updates only affect product review content.
  Internal (API Leak): productReviewPDemoteSite operates at domain level. A site's overall review quality drags down or lifts up all review pages on that domain.
- Public (Google Public): Affiliate links don't affect review quality signals.
  Internal (API Leak + DOJ): The review classification system (productReviewPReviewPage) explicitly identifies review content. Thin affiliate reviews trigger productReviewPDemotePage, while genuinely helpful reviews earn UHQ status.
