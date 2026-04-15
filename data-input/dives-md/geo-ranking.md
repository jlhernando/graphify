# Geographic Ranking: How Location Shapes Every Search Result

## Key Insight
Geographic ranking operates at three distinct levels: (1) User-level: NavBoostLocationSlice segments click data by geography. The same page accumulates separate click scores per country/region. A page performing well in Germany gets boosted for German users but not for French users. (2) Document-level: geotopicality measures alignment between page content and query location. (3) Query-level: Implicit locale detection (Patent US8200694) identifies queries with geographic intent without explicit location terms. 'Pharmacy hours' implies local intent. This means location personalization is always active: IP-based customization applies to all users, even for 'non-local' queries.

### Signals, Core (doj)
- geotopicality (Geo system, LocalWWWInfo module) - Geographic relevance score measuring alignment between page content and query location. Applies to organic ranking, not just local business results. Core geographic signal for all web pages
- NavBoostLocationSlice (NavBoost, CrapsCrapsData) - NavBoost contextually slices click data by geographic location, providing location-specific ranking adjustments based on regional user behavior patterns. Same page ranks differently in different cities based on LOCAL click patterns
- brickAndMortarStrengthScore (Geo, LocalWWWInfo) - Physical business presence authority score for local search ranking. Pure online businesses get zero local boost. Based on Google Business Profile verification, profile completeness, NAP consistency
- IP address logging (Mehta FOF #122): 'Google logs IP addresses and uses them to customize search results.' Lehman: 'Knowing a person's location can sometimes help understand what it is they're looking for'
- Nayak on location slicing (PXR0357): 'Navboost, a measure of how frequently users (subset by location and device type) click on a particular document for a particular query is a traditional signal'
- Localization in metrics (UPX0192, Lehman): 'Importance of freshness and localization built into the metric.' Location is embedded in evaluation methodology, not just applied as a filter

### API Leak: Geographic Signals (leak)
- locationIdentity - Demotes pages targeting non-matching geographic areas. Penalizes fake local content and programmatic location pages. Triggers on pages with no genuine local presence
- geoLocationQuality - Location data quality score. Measures accuracy and consistency of geographic information on a page
- geoFeatureType - Geographic feature type classification (city, state, country, landmark, etc.)
- localityScore - Geographic locality relevance measuring how locally relevant a page is
- localAuthorityScore - Local content authority measurement. Separate from domain authority. Built through local links: newspapers, chambers of commerce, local directories
- isCovidLocalAuthority - Topic-specific local authority flag for COVID-related content. Confirms topic-specific geographic authority signals exist
- isElectionAuthority - Topic-specific local authority flag for election content

### 6 Patents (patent)
- Implicit locale detection (US8200694, Pang, Kumar, 2008): Identifies queries with implicit local or regional intent without explicit location terms. 'Pharmacy hours' implies local intent regardless of user typing location. Enables geographic ranking for queries that don't mention a place
- Geographic short text language detection (US8548797, Cucerzan, 2009): Solves language detection in short text using geographic information. A short query from Spain is more likely Spanish than Portuguese; same query from Brazil is more likely Portuguese
- Location quality inference (US9558210): Inferring location quality from travel time investment. Compares anticipated vs actual distance users travel to visit a location
- N-gram language identification (US6167369, 1998): Character N-gram frequency analysis for per-document language detection. Determines which language index a page enters. Detected language can override hreflang declarations
- Search query results based on topic (US8620951): Topic-based result selection incorporating geographic relevance
- Generating local search results: Patents covering local organic result integration with standard web search (Venice update foundation)

### How Geographic Signals Affect Rankings (exploit)
- Every search is location-influenced: IP-based location customization applies to all users, all queries. Even 'non-local' queries like 'best laptop 2026' may rank differently by country due to NavBoostLocationSlice regional click patterns. There is no truly location-neutral search result
- Click data diverges by region: NavBoostLocationSlice means a page performing well with users in one region gets boosted there but not elsewhere. Building regional user engagement (not just global engagement) matters for geographic ranking
- Fake local content is detectable: locationIdentity demotes pages targeting non-matching geographic areas. Programmatic location pages ('plumber in [city]' generated at scale) without genuine local presence trigger penalties. Authentic local presence outperforms templated geographic targeting
- Local authority is separate from domain authority: localAuthorityScore is built through local links (newspapers, chambers of commerce, local directories). A site with high domain authority but no local links may rank poorly for location-specific queries. Local link building is distinct from general link building
- Physical presence creates ranking advantage: brickAndMortarStrengthScore gives physical businesses a ranking boost that pure online businesses cannot access. For local queries, having a verified Google Business Profile matters for organic ranking, not just map pack
- Implicit locale detection broadens geographic influence: US8200694 means queries without explicit locations still trigger geographic ranking. 'Restaurant reservations' implies local intent. Content must be geographically relevant even for seemingly generic queries
- Language detection overrides hreflang: US6167369 N-gram analysis determines a page's language independently of hreflang declarations. If your page's detected language doesn't match your hreflang, the detected language wins. Content language must actually match your target locale

### Calculations
- Geotopicality Score: Measures alignment between page content and query location. The Geo system's LocalWWWInfo module computes a relevance score based on geographic entities mentioned in content, page metadata, linking patterns from local sources, and user engagement data from that region. Affects: Affected by: geographic entity mentions, local link sources, regional user engagement, location metadata accuracy.
- NavBoostLocationSlice: NavBoost segments click data by geographic location. Each URL accumulates separate click quality metrics (goodClicks, badClicks, lastLongestClicks) per region. A page that users in Germany find satisfying gets boosted for German searches but receives no boost for French searches, even if both regions share a language. Affects: Affected by: regional user engagement, geographic click patterns, local user satisfaction. Regional traffic matters, not just global traffic.
- Implicit Locale Detection: Identifies queries with implicit geographic intent without explicit location terms. Analyzes query patterns, user location, and query category to determine whether results should be geographically biased. 'Pharmacy hours' triggers local intent. 'Bundesliga scores' triggers German content preference. The classification happens before retrieval, affecting which documents are even considered. Affects: Affected by: query category, user location, query pattern matching. Determines geographic bias before ranking begins.

### Geographic Ranking Pipeline
- User Location Detection: IP address logged (Mehta FOF #122). GPS/device location when available. Browser language and locale detected. Applies to all users, all queries.
- Implicit Locale Classification: QUS classifies query for geographic intent (US8200694). 'Pharmacy hours' triggers local, 'best laptop' may trigger country-level. No explicit location needed.
- Retrieval with Geographic Bias: Mustang retrieves candidates with geographic weighting. geotopicality influences which documents enter the candidate set.
- NavBoost Location Slicing: Click data segmented by user's region (NavBoostLocationSlice). Same URL has different click profiles per region. Regional engagement determines boost/demotion.
- Local Authority Scoring: localAuthorityScore computed from local link sources. brickAndMortarStrengthScore applied for physical businesses. locationIdentity penalizes fake local content.
- SERP Assembly: Tangram assembles location-appropriate SERP: local pack (if triggered), location-specific organic results, regional featured snippets, geographically relevant ads.

### Timeline
- 2003 (event): Google Local Launched - Google Local provides location-based search results. Geographic signals begin influencing organic rankings.
- 2008 (patent): Implicit Locale Detection (US8200694) - Patent for identifying queries with implicit geographic intent without explicit location terms. Foundation for geographic ranking of all queries.
- 2012 (event): Venice Update - Local results integrated into organic SERPs. Location-specific queries receive localized organic results, not just map pack listings.
- 2014 (event): Pigeon Update - Major local algorithm update. Distance and location prominence become stronger ranking factors. Local and organic signals more tightly integrated.
- 2017 (event): Possum Update - Diversifies local results by business address. Physical location proximity becomes more influential. locationIdentity signal likely strengthened.
- 2023 (doj): DOJ: IP Logging for Customization - Mehta FOF #122: 'Google logs IP addresses and uses them to customize search results.' Confirms location-based customization applies to all users.
- 2024 (leak): Geographic Signals Exposed - API leak reveals geotopicality, locationIdentity, geoLocationQuality, localityScore, localAuthorityScore, brickAndMortarStrengthScore, and topic-specific authority flags.

### Contradictions
- Public (Google Search Central (ranking documentation)): Rankings are based on relevance, quality, and usefulness. Location is one of many factors considered for local queries. vs Internal (DOJ: NavBoostLocationSlice + Mehta FOF #122 + geotopicality): Location is not 'one of many factors for local queries.' It is a pervasive signal affecting ALL searches. NavBoostLocationSlice segments click data by region for every URL. IP addresses are logged and used to customize results (Mehta FOF #122). geotopicality applies to all web pages. Implicit locale detection (US8200694) triggers geographic ranking even for queries without location terms.
- Public (Google (public guidance on local SEO)): Create content that is locally relevant. Ensure your Google Business Profile is complete and accurate. vs Internal (API Leak: locationIdentity + localAuthorityScore): locationIdentity actively demotes pages targeting non-matching geographic areas, penalizing programmatic location pages without genuine local presence. localAuthorityScore is a separate signal from domain authority, requiring local link sources (newspapers, chambers of commerce). Simply 'creating locally relevant content' is insufficient; Google measures authentic local presence through distinct signals that template pages cannot fake.
- Public (Google (public statements on equal treatment)): Google Search provides the same ranking algorithm to all users. Results are based on the query and document quality. vs Internal (DOJ: NavBoostLocationSlice + IP customization + implicit locale): Two users in different cities searching the same query receive meaningfully different results. NavBoostLocationSlice creates separate click profiles per region. IP-based customization applies before ranking. The same page with the same quality can rank #1 in one city and #20 in another based purely on regional click data. The algorithm is the same; the inputs are location-dependent.

### Overview
Geographic signals affect ALL Google searches, not just local business queries. The DOJ confirmed that Google logs IP addresses to 'customize search results' (Mehta FOF #122) and NavBoostLocationSlice segments click data by geographic location, meaning the same URL ranks differently in different cities based on regional user behavior. The Geo system provides geotopicality (content-location alignment) and brickAndMortarStrengthScore (physical business presence). Since Venice (2012), geographic results are integrated into organic SERPs. Location is always a factor, even for seemingly non-local queries.
