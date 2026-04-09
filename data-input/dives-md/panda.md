# Panda: Content Quality Algorithm

## Key Insight
Panda's core metric is the IL/RQ ratio (Independent Links / Reference Queries), measuring whether a page earns links from diverse domains relative to how often it's queried. The system has three demotion tiers: pandaDemotion (full), babyPandaDemotion (lighter, for borderline content), and babyPandaV2Demotion (targeting rendered content). A separate lowQuality ML signal uses site-to-vector embedding to classify overall content quality level.

### Signals Confirmed (leak)
- pandaDemotion - Quality modifier demotion using IL/RQ ratio
- independentLinks - Count of linking root domains (IL in ratio)
- referenceQueries - Queries previously categorized as referring to a resource (RQ)
- Panda - Core content quality algorithm module
- babyPandaDemotion - Lighter demotion for borderline content
- babyPandaV2Demotion - V2 iteration targeting rendered content quality

### Signals,  Core (doj)
- pandaDemotion - Site-wide demotion for low-quality content prevalence (core)
- babyPandaDemotion - Early Panda iteration demotion signal
- babyPandaV2Demotion - Later evolution targeting rendered content quality
- lowQuality - ML-based quality score using site-to-vector embedding
- All stored in CompressedQualitySignals module
- Operates at site level, demoting entire domains

### Quality Assessment Framework (patent)
- Site-level content quality scoring pioneered by Panda in 2011
- Independent Links as diversity metric for link quality
- Reference Queries measure brand/navigational query volume
- IL/RQ ratio: sites with links but few branded searches penalized
- Phrase-based quality prediction - Patent US9767157 (Navneet Panda, Yun Zhou, filed 2013): Predicts quality scores for new/unseen sites using phrase frequency models built from previously scored sites. Analyzes 2-gram to 5-gram phrase frequencies across all pages on a site. Maps phrase-specific relative frequency (pages containing phrase / total pages) to baseline quality scores. Smoothed scoring: aggregate_score * alpha + neutral_score * (1-alpha), where alpha can be a sigmoid function of phrase frequency. This is how Panda assigns quality scores to sites it hasn't explicitly evaluated
- Three-tier demotion system allows graduated quality penalties
- Evolution from periodic updates to continuous core algorithm

### How Panda Affects Your SEO (exploit)
- Optimize your IL/RQ ratio before anything else: Panda's core metric is Independent Links divided by Reference Queries. A high IL with low RQ (many linking domains but few branded searches) signals potential link manipulation. A low IL with high RQ (brand demand but few editorial links) signals low authority. Build both: earn diverse editorial links AND grow branded search demand through PR, social, and brand marketing simultaneously.
- Prune thin content to avoid site-wide pandaDemotion: pandaDemotion operates at the site level, meaning a high ratio of low-quality pages to total pages demotes your entire domain. Audit your site for thin pages (tag pages, empty category pages, auto-generated archives, placeholder content) and either enrich them with substantial content, noindex them, or remove them entirely. The ratio of quality pages to total indexed pages matters.
- Differentiate babyPanda triggers from full Panda: babyPandaDemotion is a lighter penalty for borderline content, while babyPandaV2Demotion specifically targets rendered content quality. Pages that look acceptable in HTML but render poorly (broken layouts, missing images, JS errors) can trigger V2. Test how your pages actually render in a headless browser to catch V2-specific issues that static HTML analysis misses.
- Avoid near-duplicate detection via shingleInfo: WebMirror uses overlapping word sequences (5-gram shingling) to detect near-duplicate content. Even heavy paraphrasing of existing content may be detected if the sentence structure and word sequence patterns overlap significantly. Create genuinely original content rather than rewriting competitors' articles. Use unique data, original reporting, or novel analytical frameworks.
- Build lowQuality immunity through site2Vec positioning: The lowQuality ML signal uses site-to-vector embedding to classify overall content quality level. Your site's position in embedding space is compared against quality models. Sites that cluster near known low-quality patterns (content farms, thin affiliate sites, ad-heavy directories) get classified negatively. Differentiate your site's content patterns from these archetypes through depth, expertise, and editorial standards.
- Treat Panda as still active despite public claims: Google publicly stated Panda was replaced by the Helpful Content System in 2022, but the API leak confirms pandaDemotion, babyPandaDemotion, and babyPandaV2Demotion are all active signals in 2024 within the QualityPandaModifier module. Optimize for both Panda's measurable ratio-based signals AND HCU's broader quality assessment. They run in parallel, not as replacements.

### pandaDemotion (DOJ)
Site-wide demotion signal for low-quality content prevalence. Direct implementation of Panda algorithm. Part of CompressedQualitySignals. Applied at site level.
Formula: pandaDemotion(site) = qualityRatio(
  lowQualityPages / totalPages
)  // CompressedQualitySignals, site-level
if ratio > threshold: demote(site, tier=full)
// too many thin pages penalizes entire domain
Patents: US8682892 (content quality scoring), US9697259 (document quality)
Affects: Affected by: overall site content quality. Too many low-quality pages demotes entire site. Prune thin/low-quality content.

### lowQuality (DOJ)
Uses site-to-vector embedding to classify overall content quality level. ML-based assessment. Part of CompressedQualitySignals.
Formula: lowQuality(site) = classify(
  site2vec(site), qualityModel
)  // ML classification via site embedding
// thin content, excessive ads, poor writing detected
Affects: Affected by: content quality patterns detectable by ML. Thin content, excessive ads, poor writing trigger this.

### independentLinks (Leak)
Count of linking root domains. Part of Panda modifier factor (IL/RQ ratio). Higher independent link count relative to other metrics = better.
Formula: pandaModifier(page) = IL / RQ
  // IL = independentLinks (unique linking root domains)
  // RQ = referenceQueries (branded/navigational queries)
if IL high && RQ low: suspect link manipulation
Affects: Affected by: diverse, natural link profile from many independent domains. Quantity of unique linking domains matters.

### Phrase-Based Quality Prediction (Patent US9767157 (Navneet Panda, Yun Zhou, 2013))
Predicts quality scores for new or unseen websites using phrase frequency models built from previously evaluated sites. The system analyzes n-gram (2-gram to 5-gram) phrase frequencies across all pages on a site, computing relative frequency as (pages containing phrase / total site pages). These frequencies are mapped to baseline quality scores derived from previously scored sites. A smoothing function prevents extreme scores: smoothed_score = aggregate * alpha + neutral * (1 - alpha), where alpha can be a sigmoid of phrase frequency quotient. This is the mechanism behind Panda's ability to score sites it hasn't explicitly rated, using statistical patterns from evaluated sites as templates.
Formula: PhraseRelFreq(phrase, site) =
  pages_containing / total_pages

PhraseQuality(phrase) = model(
  relFreq → baseline_score
)  // learned from previously scored sites
// 2-gram to 5-gram phrase analysis

SmoothedScore = aggregate × α +
  neutral × (1 - α)
// α = 0.90, 0.80, 0.75, 0.60 or sigmoid(freq_quotient)
// smoothing prevents extreme scores for sparse data
Patent: US9767157 (Predicting site quality, Panda & Zhou, 2013)
Affects: Affected by: phrase diversity and usage patterns across your site. Sites with phrase patterns matching known high-quality sites score better. Repetitive, formulaic content patterns that match low-quality templates are penalized.

### shingleInfo (DOJ)
Overlapping word sequences (shingling) detect near-duplicate pages. Identifies content copied or heavily paraphrased from other sources. Part of WebMirror system.
Formula: shingleInfo(doc) = fingerprint(
  nGrams(doc, n=5)
).compare(webIndex)  // WebMirror near-duplicate detection
if overlap > dupThreshold: flag(nearDuplicate)
Affects: Affected by: original content. Avoid duplicating content from other sites. Even heavy paraphrasing may be detected.

## Pipeline
- **Content Crawl**: Pages analyzed for content quality, depth, originality
- **IL/RQ Ratio**: independentLinks / referenceQueries = quality modifier
- **Quality Tier**: pandaDemotion vs babyPanda vs babyPandaV2
- **ML Classification**: lowQuality signal via site-to-vector embedding model
- **Site Demotion**: CompressedQualitySignals applied to all pages on domain

## Timeline
- 2011: Panda Algorithm Launches - Major update targeting content farms. Affects ~12% of English queries. Named after engineer Navneet Panda.
- 2012: Panda Iterations - Multiple Panda updates (2.0-4.0) refine quality assessment. "Baby Panda" lighter version deployed for borderline content.
- 2013: Phrase-Based Quality Prediction (US9767157) - Navneet Panda and Yun Zhou patent phrase-based site quality prediction. Analyzes 2-5 gram phrase frequencies across site pages, maps to quality scores learned from previously evaluated sites. Smoothing function prevents extreme scores. Enables Panda to predict quality for unseen sites without explicit evaluation.
- 2015: Panda Into Core - Panda integrated into core ranking algorithm. No longer a periodic update, now runs continuously.
- 2018: Evolution to HCU Concepts - Panda principles evolve toward Helpful Content concepts. Site-level quality assessment becomes more granular.
- 2024: API Leak: IL/RQ Ratio - pandaDemotion confirmed using Independent Links / Reference Queries ratio. 6 Panda signals in API.
- 2024: DOJ Trial: Quality Demotion - pandaDemotion, babyPandaDemotion, babyPandaV2Demotion, lowQuality confirmed in CompressedQualitySignals.

## Contradictions
- Public (Google Public): "Panda was replaced by the Helpful Content System in 2022."
  Internal (API Leak): pandaDemotion, babyPandaDemotion, and babyPandaV2Demotion are all active signals in 2024. The QualityPandaModifier module is still operational. Panda was never removed, it runs alongside HCU.
- Public (Google Public): "We evaluate individual pages on their own merits."
  Internal (API Leak + DOJ): Panda explicitly operates at the site level. pandaDemotion is a "site-wide demotion signal for low-quality content prevalence." One bad section can demote all pages on a domain.
- Public (Google Public): "Focus on creating helpful content. Specific metrics don't matter."
  Internal (API Leak): The IL/RQ ratio is a precise, measurable formula. independentLinks (unique linking domains) divided by referenceQueries (branded search volume). This specific ratio drives Panda demotion.
