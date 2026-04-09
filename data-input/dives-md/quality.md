# Q*: Google's Site Quality Scoring System

## Key Insight
Q* is a hand-crafted, query-independent, site-wide quality score on a 0-1 scale. Sites below 0.4 are ineligible for featured snippets, People Also Ask, and rich results. This threshold was confirmed by both sworn DOJ testimony (HJ Kim) and independently by the Candour Agency exploit across 800K domains. Google publicly denied "domain authority" from 2016-2019 while Q* existed internally.

### Q* and Its Components (doj)
- Q* - 0-1 site quality score. Hand-crafted, not ML. Primarily query-independent, but HJ Kim (PXR0356, Feb 2025) revealed Q* can be query-dependent in certain cases. Below 0.4 = no rich results.
- HJ Kim created page quality team (PXR0356): Kim built the page quality team ~2008 as his 3rd major project after NavBoost and another signal. Hand-crafting philosophy: 'if anything breaks Google knows what to fix' vs ML black boxes
- NSR - Normalised Site Rank. Site-level quality successor to PageRank.
- siteAuthority - Persistent domain trust score from quality_nsr module.
- authorityPromotion - Active boost applied to high-authority domains in Q*.
- chard - YMYL-specific authority signal for health, finance, safety topics.
- ewok / keto - Internal codename quality prediction models feeding NSR.
- Google vs Bing quality gap (UPX0220, Desktop Search Comparative Jul 2020): On 500 popular US queries, Google significantly better on full-page SxS (+0.185). 'Google is relatively stronger in ranking, authoritativeness, local context, interpretation, topicality.' Bing stronger on 'feature diversity and interactive results.' Location data was critical differentiator: 15 of 20 top SxS differences needed local context
- UGC demotion as quality lever (PXR0037, Board Update Q2 2024): AI Overviews quality improved by 'increased quality of inputs and reduced the prevalence of UGC and joke/satirical content.' Confirms active filtering of user-generated and satirical content from AI Overview inputs
- Ads revenue preservation as AIO principle (PXR0109, Gemini in Search exec review): 'Preserve ads revenue' listed as explicit design principle alongside 'preserve a healthy ecosystem.' AIO design balances user value with revenue protection
- Authoritative Voices as ranking philosophy (PXR0034, Evolution of Search Sep 2023): Magi framework lists 'Perspectives from Human and Authoritative Voices' as a search superpower. Authority signals are a core ranking value, not just a technical signal
- Content farms as Q* origin (PXR0356, HJ Kim Interview Feb 2025): 'Content farms paid students 50 cents per article and they wrote 1000s of articles on each topic. Google had a huge problem with that. That's why Google started the team to figure out the authoritative source.' Content farms were the direct catalyst for creating the page quality team
- Q* is 'incredibly important' (PXR0356): HJ Kim: 'Quality score is hugely important even today. Page quality is something people complain about the most.' Q* is largely static and largely related to the site rather than the query
- Q* could be reverse engineered (PXR0356): HJ Kim acknowledged Q* 'could be easily reverse engineered because Q is largely static.' Static nature makes it vulnerable to external measurement (as Candour demonstrated)
- PageRank as input to Q* (PXR0356): PageRank described as 'a single signal relating to distance from a known good source,' used as one input feeding into the Quality score
- 18 search quality aspects (UPX0204, Eric Lehman Nov 2018): Internal document lists 18 quality dimensions: Relevance, Page quality, Popularity, Freshness, Localization, Language, Centrality, Topical diversity, Personalization, Web ecosystem, Mobile friendly, Social fairness, Optionalization, Porn demotion, Spam, Authority, Privacy, User control of spell correction
- User behavior as tenuous quality proxy (UPX0204): 'The association between observed user behavior and search result quality is tenuous.' Internal acknowledgment that clicks and engagement are imperfect measures of actual quality
- Search/Ads wall pressure (UPX0708, Haahr-Nayak Staff Chat Jun 2020): Paul Haahr: 'there can be pressure via Ads to change the product to favor Ads in general more - not individual advertisers, but, say, holding back Search changes that are good for users but bad for revenue.' Haahr warned: 'relying on people rather than institutional structures isn't necessarily a good idea for the long haul'
- Newsroom analogy for Search independence (UPX0708): Danny Sullivan compared Search to a newsroom with an editor-in-chief providing protection against ads pushing for things. Institutional tension between search quality and revenue optimization acknowledged internally
- hostAge sandbox attribute (API leak, confirmed in DOJ): 16-bit integer calculated as day number after 2005-12-31. Purpose: 'Sandbox fresh spam in serving time.' Used as a twiddler during ranking. Sandbox period approximately 3-6 months for new sites; established domains bypass the constraint. Confirms Google does sandbox new sites despite public denials
- IS score = 'approximation of user utility' (Nayak testimony): IS (Information Satisfaction) described as primary quality metric. Derived from approximately 16,000 human raters. 'Always remains human-derived.' Combined with search log data for RankEmbed training. Wikipedia removal would cause approximately 0.5 IS point loss. IS4 is the side-by-side variant comparing Google vs competitors
- Code Yellow 2019: ads pressure on search quality (Ben Gomes testimony, Oct 2023): Google declared 'Code Yellow' emergency in Feb 2019 lasting 7 weeks when query growth slowed, threatening revenue targets. Gomes: 'I think we are getting too involved with ads for the good of the product and company.' 'We could increase queries quite easily in the short term in user negative ways (turn off spell correction, turn off ranking improvement).' Search and Chrome engineers were reassigned to ads teams. Gomes departed his role approximately one year later
- Ad auction manipulation affects quality perception (Jerry Dischler VP Ads testimony, Sep 2023): 'Squashing' artificially raises prices against highest bidders. RGSP (2019) randomly assigns top slots to second-highest bidders for 10% revenue increase. Project Momiji: 15% price inflation per auction. 'We tend not to tell advertisers about pricing changes.' Search ad CPCs more than doubled 2013-2020. Demonstrates profit optimization overriding stated auction transparency. 'Shaking the cushions' to meet revenue targets
- lowQuality (API leak): NSR-derived low quality score. Feeds into Q* composite as a negative signal for sites with poor content quality metrics.
- scamness (API leak): Scam model scoring on 0-1023 range. Detects deceptive or fraudulent site patterns. High scamness triggers severe ranking penalties.
- unauthoritativeScore (API leak): Authority deficit measurement. Quantifies how far a site falls below authority expectations for its topic space.
- pandaDemotion (API leak): Content quality adjustments inherited from Panda algorithm. Site-wide demotion factor applied to domains with thin or low-value content.

### Page-Level Quality (leak)
- OriginalContentScore - 7-bit scoring (0-127) measuring content originality.
- EffortScore - LLM-estimated content creation effort.
- gibberishScores - Detection of spun/nonsensical/AI-generated content.
- vlq - Very Low Quality flag. Severe site-level penalty.
- clutterScore - Tracks excessive ads, popups, UI noise.
- ymylNewsV2Score - YMYL health/finance content authority.

### Scoring New Sites (exploit)
- Candour endpoint exploit - Discovered by Mark Williams-Cook (Candour Agency). Google endpoint exposed 2TB of data across 90M queries. $13,337 bounty via Google Vulnerability Reward Program. Over 2,000 properties revealed.
- PredictingSiteQuality - Phrase model converts content into numerical "shape" and compares to known-quality sites.
- Explains why AI content initially ranked: LLM output matched "good content" patterns.
- Site Quality Score Patent - Calculated from brand search volume, click-through selection rate, and anchor text brand mentions.
- Panda IL/RQ ratio - Independent links divided by reference queries as quality modifier.
- SiteQualityThreshold - 0.4 cutoff confirmed across 800K domains (subdomain-level scoring).
- Q* threshold at subdomain level - Sites below 0.4 ineligible for featured snippets, PAA, rich results. Confirmed at subdomain granularity, not root domain.
- Q* calculation factors - Brand search frequency, click-through rate when NOT in position 1, anchor text brand mentions across the web.
- Q* as 'heat race qualifier' - High relevance cannot overcome low quality scores. Explains why authoritative domains dominate regardless of page optimization. Quality gating precedes relevance ranking.

### Consensus Scoring (Candour Exploit) (exploit)
- Google counts passages within content that agree with, contradict, or remain neutral to 'general consensus' on a topic
- Generates a consensus score impacting ranking, especially for debunking queries (e.g., 'Is the earth flat?')
- Political topics intentionally maintain mixed viewpoints in results
- YMYL queries (health, financial) receive different ranking criteria and stricter evaluation standards
- Consensus scoring likely connects to the API leak's 'topicEmbeddings' and factuality signals

### Q* (DOJ: 0-1 scale, hand-crafted, 0.4 threshold)
Site-wide score on 0-1 scale. Hand-crafted (not ML). Operates at subdomain level. Below 0.4 = ineligible for featured snippets. Inputs include site trust, content quality, E-E-A-T indicators. Primarily query-independent, but HJ Kim (PXR0356, Feb 2025) revealed Q* can be query-dependent in certain cases. Largely static, doesn't change with every query.
Formula: Q*(site) = hand_crafted(
  seed_distance, engagement, E-E-A-T
)  // range: 0.0 - 1.0, query-independent
if Q* < 0.4: block featured_snippets, rich_results
// subdomain-level, NOT ML (HJ Kim deposition)
Patents: US9697259 (document quality signals), US9165040 (link graph distances)
Affects: Affected by: overall domain trustworthiness and expertise. Changes slowly. Must be above 0.4 for rich SERP features.

### IS Score (DOJ: Information Satisfaction, 100-point scale)
100-point scale computed from 16,000 human raters worldwide. Used to train RankBrain, SpamBrain, Helpful Content System, MUM. Wikipedia removal would cause ~0.5 IS point loss. Mehta opinion (FOF 134): In 2020, Google tested degrading large ranking components (NavBoost, Synonyms) by 1 IS point for 3 months. Result: only 0.66-0.99% decline in global search revenue. This proves significant quality depreciation would NOT cause significant revenue loss. Nayak caveat: larger IS changes may have nonlinear effects. IS4 is the side-by-side rating system using human raters to compare Google vs competitors (Mehta FOF 136).
Formula: IS(query) = mean(
  rater_scores[1..16000]
)  // range: 0 - 100
// training target for RankBrain, SpamBrain, HCU, MUM
// Wikipedia removal = ~0.5 IS point loss
// 1 IS point decline = 0.66-0.99% revenue decline (Mehta FOF 134)
// IS4: side-by-side rater comparison vs competitors
Affects: Affected by: content that human evaluators rate highly. Aligns with E-E-A-T quality guidelines. IS score trains the ML models that rank pages. 1 IS point = ~0.66-0.99% search revenue.

### contentEffort (DOJ: LLM-based labor and originality estimation)
Uses LLM to estimate labor and originality invested in content creation. Part of CompressedQualitySignals module. Measures creative effort vs automated/templated content.
Formula: contentEffort(page) = LLM_score(
  labor, originality, expertise_depth
)  // in CompressedQualitySignals module
// distinguishes expert writing from templated/AI content
Patents: US8682892 (content quality scoring), US9697259 (document quality signals)
Affects: Affected by: original research, unique data, expert analysis. LLM detects boilerplate vs genuine effort. More original content = higher score.

### OriginalContentScore (Leak: 7-bit value 0-127 + DOJ)
7-bit value (0-127 range) in API leak. Measures uniqueness of content relative to the broader web. Assesses originality of a document. Low scores indicate scraped or heavily duplicated content.
Formula: OriginalContentScore(page) = uniqueness(
  content, web_corpus
)  // 7-bit: range 0 - 127
if score < threshold: flag as scraped/duplicate
// low score = heavily duplicated content
Patents: US8682892 (content quality scoring)
Affects: Affected by: publishing original content not found elsewhere. Avoid scraping/rewriting. Unique data, perspectives, and analysis score higher.

## Pipeline
- **Content Analysis**: OriginalContent, Effort, gibberish, YMYL
- **NSR / siteAuthority**: Site-level aggregation of quality signals
- **Q* Score (0-1)**: Hand-crafted composite, query-independent
- **Gating (0.4)**: Below 0.4: no featured snippets, PAAs, rich results
- **Ascorer Input**: Feeds into primary ranking alongside P*, T*

## Timeline
- 2008: Page Quality Team Founded - HJ Kim started the page quality team to combat content farms. 'Content farms paid students 50 cents per article and they wrote 1000s of articles on each topic.' The team's mission: figure out the authoritative source. (PXR0356)
- 2011: Panda Algorithm - First site-wide quality scoring. IL/RQ ratio (independent links / reference queries) as quality proxy.
- 2013: Site Quality Score Patent - Google patent on predicting site quality from content patterns and brand search volume.
- 2018: E-A-T Framework Formalized - Expertise, Authoritativeness, Trustworthiness become central to quality rater guidelines.
- 2022: Helpful Content Update - Site-wide quality classifier. HCU system penalizes "unhelpful" content at domain level.
- 2024: API Leak: Q* Confirmed - Q* aggregate quality score found in ContentWarehouseAPI. NSR, siteAuthority, chard scores exposed.
- 2024: Candour: Q* Threshold & Consensus Scoring - Mark Williams-Cook (Candour Agency) discovered Google endpoint exposing 2TB data across 90M queries. Confirmed 0.4 Q* threshold at subdomain level. Revealed consensus scoring, click probability prediction, and 8 query semantic classes. Google paid $13,337 bounty.
- 2017: Project Folly: Latency Response (Mehta FOF 137) - Google analyzed latency vs Bing: for popular queries, 25% of SERPs took >3 seconds. Bing's first result arrived sooner 98% of the time, ~300ms faster. Google launched Project Folly to decrease latency. 'The project was a success' (Nayak). Demonstrates Google monitors and responds to competitor quality metrics.
- 2020: Quality Reduction Experiment (Mehta FOF 134) - Google tested degrading large ranking components (NavBoost, Synonyms) by 1 IS point for ~3 months. 1 IS point = loss of 2x all Wikipedia content. Result: only 0.66-0.99% global revenue decline. Proves Google's monopoly is insulated: even significant quality degradation barely affects revenue. Nayak caveat: larger changes may be nonlinear with 'inflection points where if you make search much worse, you might actually lose a lot more traffic.'
- 2019: Code Yellow: Ads Pressure on Search (Ben Gomes) - Feb 2019: Google declared Code Yellow emergency (7 weeks) when query growth slowed. Ben Gomes: 'I am getting concerned that growth is all we are thinking about.' Search/Chrome engineers reassigned to ads. Gomes warned degrading ranking quality could boost short-term queries. Departed role ~1 year later. Demonstrates tension between search quality and revenue optimization.
- 2024: hostAge Sandbox Confirmed (API Leak + DOJ) - API leak exposed hostAge attribute: 16-bit integer, day number after 2005-12-31 epoch. Purpose: 'Sandbox fresh spam in serving time.' Sandbox period 3-6 months for new sites. Google publicly denied sandbox existence while the system was active.
- 2024-2025: DOJ: Hand-Crafted Quality - HJ Kim: Q* is hand-crafted, not ML. Nayak: quality is static and site-wide. 0.4 disqualifies from featured snippets.
- 2025: DOJ Remedies: Q* Can Be Query-Dependent (PXR0356) - HJ Kim interview (Feb 2025): Created page quality team ~2008. Revealed Q* can be query-dependent in certain cases, contradicting prior understanding that it was purely static. Hand-crafting philosophy: 'if anything breaks Google knows what to fix.' API leak acknowledged: 'documents named components but don't go into curves/thresholds.'
- 2018: 18 Quality Aspects Defined (UPX0204) - Eric Lehman's 'Ranking for Research' document defines 18 search quality aspects: Relevance, Page quality, Popularity, Freshness, Localization, Language, Centrality, Topical diversity, Personalization, Web ecosystem, Mobile friendly, Social fairness, Optionalization, Porn demotion, Spam, Authority, Privacy, User control of spell correction. Admits user behavior is a 'tenuous' quality proxy.
- 2020: Search/Ads Wall Pressure (UPX0708) - Paul Haahr to Pandu Nayak: 'there can be pressure via Ads to change the product to favor Ads in general more - not individual advertisers, but, say, holding back Search changes that are good for users but bad for revenue.' Warned institutional structures needed, not just reliance on individuals.

## Contradictions
- Public (Google Public (2016-2019)): "There is no domain authority. We don't have such a metric. We evaluate pages individually."
  Internal (Internal Reality): Q* is a site-wide, domain-level quality score. siteAuthority is a persistent domain trust metric. Both existed during Google's public denials.
- Public (Google Public): "Our algorithms don't distinguish between subdomains and root domains."
  Internal (Candour Exploit): Site quality score operates at subdomain level. Different subdomains of the same root domain receive different quality scores (confirmed across 800K domains).
- Public (Google Public): "We use hundreds of signals and machine learning to evaluate quality."
  Internal (HJ Kim Deposition): Q* is hand-crafted, not ML. Only RankBrain and DeepRank use LLM technologies. The vast majority of ranking signals are hand-crafted.
- Public (Google Public): "If our search quality declined, users would quickly switch to competitors. Competition is just a click away."
  Internal (Mehta Liability Opinion (FOF 134)): Google's own 2020 experiment: degrading large ranking components by 1 IS point (equivalent to removing 2x Wikipedia) for 3 months caused only 0.66-0.99% revenue decline. Significant quality degradation barely affects revenue due to default bias and switching costs, not because users freely choose Google.
- Public (Google Public (Gary Illyes, 2017-present)): "There is no sandbox. We don't treat new sites differently in ranking."
  Internal (API Leak + DOJ Trial Evidence): hostAge attribute (16-bit integer, days since 2005-12-31) explicitly 'sandboxes fresh spam in serving time.' Established domains bypass constraint. New sites face 3-6 month sandbox period.
- Public (Google Public): "We rely on sophisticated AI and machine learning to understand and measure content quality."
  Internal (DOJ Trial Exhibit UPX0204 (Eric Lehman, Nov 2018)): 'The association between observed user behavior and search result quality is tenuous.' Google internally admits that the behavioral signals they rely on (clicks, dwell time) are poor proxies for actual quality.
- Public (Google Public): "Search and Ads operate independently. Ads has no influence over organic search results."
  Internal (DOJ Trial Exhibit UPX0708 (Haahr-Nayak Staff Chat, Jun 2020)): Paul Haahr: 'there can be pressure via Ads to change the product to favor Ads in general more - not individual advertisers, but, say, holding back Search changes that are good for users but bad for revenue.' Protection depends on individuals, not institutional structures.
