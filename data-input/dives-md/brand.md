# Brand: Navigational Query Signals & Entity Recognition

## Key Insight
The queriesForWhichOfficial signal in PerDocData maps specific brand queries to their official URLs. When a query is classified as navigational, the OfficialPageTwiddler applies a significant ranking boost to the matched official domain, making it nearly impossible for non-brand pages to outrank the official site. This means brand recognition in Google's system is binary: either your domain is mapped as the official source for a brand query, or it is not. Google measures brand strength through directFrac (Chrome type-in traffic) and brand search volume, creating a feedback loop where established brands with more direct traffic receive stronger brand signals.

### DOJ Trial: Brand Query Mechanics (doj)
- queriesForWhichOfficial (PerDocData): Maps brand queries to official URLs. When a user searches for a brand name, this signal identifies which domain is the authoritative source.
- OfficialPageTwiddler: Twiddler function that applies a ranking boost to official brand pages for navigational queries. Confirmed in sworn testimony as a deliberate ranking override.
- Navigational query classification triggers special treatment: brand queries receive different SERP layout via Tangram, often including sitelinks, Knowledge Panel, and suppressed competitor results.
- The DOJ trial revealed that Google's brand signal advantages established brands through the DataScaleAdvantage cycle: more brand searches generate better click data, which reinforces the brand's official mapping.

### Related Leak Evidence (leak)
- No direct Brand-specific signals in the API leak, but related signals feed brand recognition:
- navigationalBoost (QueryUnderstanding): Direct ranking boost applied when a query is classified as navigational. Brand queries are the primary trigger.
- siteAuthority: Domain trust score that correlates with brand strength. Official brand domains accumulate higher siteAuthority through consistent engagement.
- intentScores: Query intent classification that distinguishes navigational (brand) from informational and transactional queries. Drives whether brand treatment is applied.
- commercialScore: Per-page commercial intent detection. Brand pages with transactional intent get different treatment than brand informational pages.

### 2 Patents (patent)
- Brand search volume as quality proxy: Patent evidence that the volume of branded searches for a domain is used as a trust and quality indicator. More brand searches signal legitimacy.
- directFrac: Chrome data measuring what fraction of a site's traffic comes from direct/type-in navigation. High directFrac indicates strong brand recognition.
- chromeInTotal: Total Chrome browser visits at site level. Combined with directFrac, provides a real-world measure of brand strength independent of search rankings.
- Entity recognition via Webref: Brand entities are linked to Knowledge Graph entries. The kgEntityId mapping determines whether a brand is a recognized entity versus just a keyword.
- Official page classification through pattern matching on domain ownership, WHOIS data, and entity-domain association in the Knowledge Graph.
- Knowledge panel triggering - Patent US10922326 (Google, filed 2012): System for triggering knowledge panels when query references a factual entity. Entity identification from query + search results, confidence scoring, multi-source data aggregation (Knowledge Graph, Wikipedia, IMDB). Triggering criteria: entity prominence, structured data availability, estimated user experience improvement. Panel content prioritized by source authority, information freshness, and frequency across sources. Critical for brand SERP presentation
- US10394816: Detecting product lines within product search queries

### How Brand Signals Affect Your SEO (exploit)
- Build branded search volume deliberately: Google uses brand search volume as a quality proxy. Run campaigns that drive people to search for your brand name specifically. Podcast mentions, PR, social media, and offline advertising all generate brand queries that strengthen your queriesForWhichOfficial mapping.
- Increase direct traffic through Chrome: directFrac and chromeInTotal measure type-in traffic via Chrome. High direct traffic ratios signal brand strength. Build email lists, browser bookmarks, and habitual visitors rather than relying solely on search traffic.
- Establish your Knowledge Graph entity: Brand recognition depends on Webref linking your brand to a Knowledge Graph entity. Use Schema.org Organization markup, maintain a consistent brand presence across authoritative sources, and ensure your brand appears in Wikipedia/Wikidata to establish entity status.
- Own your brand SERP: For navigational queries, Google shows sitelinks, Knowledge Panel, and social profiles. Ensure your official site has clear site structure for sitelinks generation. Claim your Google Business Profile. Link your official social accounts in structured data.
- Protect against brand query hijacking: Competitors bidding on your brand name in ads can appear above your organic result. While OfficialPageTwiddler boosts organic results, paid ads still appear above. Monitor brand SERP and consider brand bidding defensively.
- Differentiate brand vs generic content: Pages targeting branded queries should be clearly branded (homepage, about, product pages). Pages targeting generic queries should focus on topical authority instead. Google treats these query types with entirely different ranking logic.

### queriesForWhichOfficial (DOJ)
Brand signal stored in PerDocData that maps specific navigational/brand queries to their official URLs. When a user searches for a brand name, Google looks up whether a URL is the official source for that query. If matched, OfficialPageTwiddler applies a significant ranking boost.
Formula: queriesForWhichOfficial(url) = match(
  brand_queries → official_url
)  // navigational query to source mapping
if match: OfficialPageTwiddler(boost)
// official domain nearly always ranks #1 for brand queries
Patents: US8762394 (trust buttons for official sources)
Affects: Affected by: strong brand recognition, branded search volume, Knowledge Graph entity status, Schema.org Organization markup, consistent branding across the web.

### Brand Strength Score (Patent+Leak)
Composite brand strength derived from Chrome direct traffic data and branded search volume. directFrac measures what fraction of a site's visits come from direct navigation (typing the URL or using bookmarks). chromeInTotal measures total Chrome browser visits. Combined, these provide a real-world brand strength measure independent of search rankings.
Formula: BrandStrength(domain) = f(
  directFrac,      // direct/type-in traffic fraction (Chrome)
  chromeInTotal,   // total Chrome browser visits
  brand_search_vol // volume of branded queries
)
// feeds into siteAuthority and Q* quality scoring
Affects: Affected by: direct traffic campaigns, brand awareness marketing, memorable domain names, email marketing driving direct visits, offline advertising generating brand searches.

### Knowledge Panel Triggering (Patent US10922326 (Google, 2012))
System for deciding when to display a knowledge panel in the SERP for an entity query. Identifies entities from query text and search results using NER. Scores entity relevance and confidence. Evaluates panel quality based on: information completeness, source authority, data freshness, and query-entity match strength. Compares against triggering threshold. Aggregates data from multiple sources (Knowledge Graph, Wikipedia, IMDB, official websites). Panel content prioritized by: source authority, freshness, and frequency across sources. For brand entities, this determines whether a Knowledge Panel appears alongside organic results.
Formula: PanelTrigger(query) =
  entityConfidence(query) ×
  panelQuality(
    completeness,
    authority,
    freshness,
    match_strength
  )
if score > threshold:
  display knowledge panel
// multi-source: KG, Wikipedia, IMDB, official site
// content ranked by authority + freshness
Patent: US10922326 (Triggering knowledge panels, 2012)
Affects: Affected by: entity recognition in Knowledge Graph, structured data quality, Wikipedia/Wikidata presence, entity notability, multi-source coverage. Brand entities with strong KG presence trigger panels more reliably.

## Pipeline
- **Query Classification**: QUS classifies query as navigational, informational, or transactional. Brand name queries classified as navigational.
- **Entity Resolution**: Webref resolves brand name to Knowledge Graph entity (kgEntityId). Determines if brand is a recognized entity.
- **Official URL Lookup**: queriesForWhichOfficial maps brand query to official domain. PerDocData stores the brand-to-URL mapping.
- **OfficialPageTwiddler**: Twiddler applies significant ranking boost to matched official domain. Near-guaranteed #1 for branded navigational queries.
- **SERP Assembly**: Tangram assembles brand SERP: sitelinks, Knowledge Panel, social profiles, branded features.

## Timeline
- 2009: Brand Search Volume Patent - Google files patent using branded search volume as a quality and trust proxy. Sites that generate brand searches are treated as more authoritative.
- 2011: Vince Update - Major algorithm update favoring established brands for broad, competitive queries. Brands start dominating head terms in SERPs.
- 2012: Knowledge Panel Triggering Patent (US10922326) - Google patents knowledge panel triggering for entity queries. NER-based entity identification, multi-source data aggregation (KG, Wikipedia, IMDB), quality threshold for panel display. Content prioritized by authority and freshness. Foundation for brand Knowledge Panels.
- 2015: Knowledge Graph Brand Entities - Brand entities integrated into Knowledge Graph. Branded queries trigger Knowledge Panels with structured business information.
- 2019: Chrome Direct Traffic Signals - directFrac and chromeInTotal begin measuring brand strength through real browser navigation data. Direct traffic becomes a brand quality signal.
- 2024: DOJ Trial: queriesForWhichOfficial - Brand query mapping confirmed in sworn testimony. OfficialPageTwiddler revealed as deliberate brand boost mechanism. DataScaleAdvantage reinforces brand dominance.
