# Author & Brand Authority

## Key Insight
Google tracks author identity via authorObfuscatedGaiaStr, linking content creators to Google's GAIA identity system, even though authorship markup was "deprecated." The queriesForWhichOfficial signal identifies brand queries, boosting official sites. DataScaleAdvantage (DOJ testimony) confirms a virtuous cycle: more queries generate better click data, which improves ranking, which attracts more queries.

### Authority Signals (leak)
- author - Document author(s) as structured entity data
- isAuthor - Boolean for entity authorship attribution
- siteAuthority - Domain trust score feeding into Q*
- authorityPromotion - Positive ranking boost for authoritative domains
- unauthoritativeScore - Negative signal for domains lacking expertise
- topicAuthoritySignal - Topic-specific authority measurement

### Signals (doj)
- isAuthor - Distinct authorship identification on a page
- authorObfuscatedGaiaStr - Author identity linked to Google's GAIA system
- queriesForWhichOfficial - Brand query identification for official sites
- DefaultBias - Default settings exploit cognitive bias (DOJ antitrust)
- DataScaleAdvantage - More queries = better data = better ranking = more queries
- AlgorithmReconstructionRisk - Sharing data would let competitors rebuild algorithm

### 4 Patents (patent)
- directFrac - Direct/type-in traffic fraction (Chrome data)
- chromeInTotal - Total Chrome browser visits at site level
- OfficialPageTwiddler - Boosts official brand pages in rankings
- Brand search volume as quality proxy (patent evidence)
- E-A-T: entity-level expertise tied to Knowledge Graph
- Author entities linked to published works across domains
- News source quality scoring - Patent US10459926 (Google, filed 2015): Computes source authority using 13 orthogonal metrics: article volume, average length, story importance (cluster size), breaking news score (log(N1/T) for T<=3h), usage/traffic, human editorial opinion, circulation, staff size, bureau count, original named entities, coverage breadth, international diversity, writing style. NEWSCORE(D) = 0.8*OLDSCORE(D) + 0.2*SOURCERANK. Prevents lower-tier sources from outranking established news organizations
- US8126876: Systems and methods for improving the ranking of news articles; US8332382: Systems and methods for improving the ranking of news articles; US8645368: Systems and methods for improving the ranking of news articles

### How Author & Brand Authority Affects Your SEO (exploit)
- Build a recognizable author entity: Google's authorObfuscatedGaiaStr links content to the GAIA identity system. Authors with Google profiles, Knowledge Graph presence, and consistent bylines across domains get stronger entity signals. Create and maintain author pages with Schema.org Person markup, linking to all publications.
- Invest in branded search volume: The queriesForWhichOfficial signal maps brand queries to official sites, triggering the OfficialPageTwiddler boost. Run brand awareness campaigns that generate direct searches for your brand name. More brand queries = stronger official site signal.
- Increase direct traffic through Chrome: directFrac and chromeInTotal measure type-in traffic via Chrome data. Sites with high direct traffic ratios signal brand strength. Build email lists, browser bookmarks, and habitual visitors rather than relying solely on search traffic.
- Earn topicAuthoritySignal through depth, not breadth: This topic-specific authority score rewards deep coverage of a niche. Publish comprehensive content clusters within your expertise area. A site that covers 50 articles on one topic builds stronger topical authority than 50 articles across 10 topics.
- Avoid triggering unauthoritativeScore: This negative signal explicitly demotes domains lacking expertise. Avoid publishing YMYL content (health, finance, legal) without demonstrable credentials. Each low-authority page drags down the domain-wide siteAuthority that feeds into Q*.
- Leverage the authorityPromotion boost: This positive ranking signal rewards authoritative domains. Build it through consistent quality, earning links from other authoritative sites, and maintaining editorial standards. The DOJ trial confirmed siteAuthority feeds directly into Q*, meaning domain trust is a ranking gate, not just a tiebreaker.

### isAuthor (DOJ)
Binary flag in PerDocData. Google checks if the page has identifiable authorship.
Formula: isAuthor(page) = detect(
byline, author_schema, bio_link
)  // binary: true/false in PerDocData
// authorship never stopped being tracked internally
Patents: US8762394 (trust buttons for authors), US8595186 (author rank)
Affects: Affected by: clear author bylines, author bio pages, consistent authorship across articles.

### authorObfuscatedGaiaStr (DOJ)
Hashed author ID connecting to Google's internal identity graph. Links content to known author entities.
Formula: authorGaia(page) = hash(
author_entity → GAIA_identity_graph
)  // obfuscated, links to Google identity system
// connects content to known author across domains
Patents: US8595186 (author rank), US8762394 (trust buttons)
Affects: Affected by: having a Google account/profile, being recognized as an entity. Author identity connects to Knowledge Graph.

### queriesForWhichOfficial (DOJ)
Brand signal in PerDocData identifying official/authoritative sources for navigational queries.
Formula: queriesForWhichOfficial(url) = match(
brand_queries → official_url
)  // navigational query to source mapping
// OfficialPageTwiddler boosts matched pages
Patents: US8762394 (trust buttons for official sources)
Affects: Affected by: strong brand recognition. Official site markup, consistent branding, Schema.org Organization markup.

### siteAuthority (DOJ+Leak)
Domain-wide reputation score aggregated from trust signals including PageRankNS, NSR, engagement. Applied in Q* ranking.
Formula: siteAuthority(domain) = aggregate(
PageRankNS, NSR, engagement, trust
) → Q*  // feeds directly into quality scoring
// domain-wide, shared across all pages
Patents: US9165040 (link graph distances), US9400849 (scalable short paths)
Affects: Affected by: long-term domain quality, trustworthiness, consistent content quality.

### News Source Quality Score (Patent US10459926 (Google, 2015))
Computes multi-factor quality scores for news sources using 13 orthogonal metrics: article production volume, average article length, story importance (cluster size in news event grouping), breaking news score (log(N1/T) where T is time to first coverage, N1=3 hours), usage/traffic patterns, human editorial opinion, circulation statistics, staff size, bureau count, original named entities generated, coverage breadth, international diversity, and writing style quality. Metrics combined via weighted sum or percentile normalization. Final article ranking blends original IR score with source quality: NEWSCORE = alpha*OLDSCORE + beta*SOURCERANK (default alpha=0.8, beta=0.2).
Formula: BreakingScore = log(N1 / T)
// T = time to first coverage, N1 = 3 hours

SOURCERANK = weighted_sum(
  article_volume, avg_length,
  breaking_score, traffic,
  editorial_opinion, staff_size,
  bureau_count, original_entities,
  coverage_breadth, intl_diversity,
  writing_quality
)  // 13 metrics via percentile normalization

NEWSCORE(D) = 0.8 × OLDSCORE(D) +
  0.2 × SOURCERANK(SOURCE(D))
Patent: US10459926 (News article ranking, 2015)
Affects: Affected by: production consistency, breaking news speed, original reporting, staff/bureau infrastructure, writing quality. Rewards institutional news authority over individual articles.

## Pipeline
- **Author Identity**: GAIA system, isAuthor, entity linking
- **Brand Recognition**: directFrac, chromeInTotal, brand queries
- **Topic Authority**: topicAuthoritySignal, site expertise
- **Authority Score**: authorityPromotion vs unauthoritativeScore
- **Q* Integration**: siteAuthority feeds into quality score

## Timeline
- 2011: Google Authorship (rel=author) - Google launches authorship markup connecting content to Google+ profiles. Author photos appear in SERPs.
- 2014: Authorship Markup Deprecated - Google removes visible authorship from SERPs, claims it was not useful. But internal entity signals continue.
- 2015: News Source Quality Patent (US10459926) - Google patents 13-metric news source quality scoring: article volume, breaking news speed (log(N1/T)), staff size, bureau count, original named entities, coverage breadth, writing quality. NEWSCORE = 0.8*OLDSCORE + 0.2*SOURCERANK. Source authority weighted at 20% of final article score.
- 2018: E-A-T in Quality Guidelines - Expertise, Authoritativeness, Trustworthiness formalized. Author expertise becomes part of quality assessment.
- 2019: Brand Authority Signals - Patents on brand search volume as quality proxy. Direct/type-in traffic measured via Chrome.
- 2024: API Leak: Author & Authority - author, isAuthor, siteAuthority, authorityPromotion, unauthoritativeScore, topicAuthoritySignal confirmed.
- 2024: DOJ Trial: GAIA Identity - authorObfuscatedGaiaStr links authors to Google identity system. queriesForWhichOfficial identifies brand queries.

## Contradictions
- Public (Google (2014)): Authorship markup is deprecated. We don't track content authorship in ranking.
  Internal (API Leak + DOJ): author, isAuthor, and authorObfuscatedGaiaStr are active ranking signals. Author identity is linked to Google's GAIA identity system. Authorship tracking never stopped, just the public markup.
- Public (Google Public): Small sites can rank just as well as large brands.
  Internal (DOJ Trial): DataScaleAdvantage creates a virtuous cycle favoring established sites. DefaultBias through browser/OS defaults drives traffic to Google, which reinforces ranking data for already-popular sites.
- Public (Google Public): E-A-T is a concept from quality rater guidelines, not a direct ranking factor.
  Internal (API Leak): authorityPromotion, unauthoritativeScore, and topicAuthoritySignal are explicit algorithmic signals. siteAuthority directly feeds into Q*. E-A-T is coded into the algorithm.
