# Local & Geographic Ranking

## Key Insight
NavBoost click data is sliced by geographic location (NavBoostLocationSlice), meaning the same page can rank differently in different cities based on local click patterns. brickAndMortarStrengthScore measures physical business presence for local queries. locationIdentity demotes pages targeting non-matching geographic areas, penalizing fake local content.

### Geographic Signals (leak)
- geoFeatureType - Geographic feature type classification
- geoLocationQuality - Location data quality score
- localAuthorityScore - Local content authority measurement
- localityScore - Geographic locality relevance
- locationIdentity - Demotion for non-matching geo targeting
- isCovidLocalAuthority - COVID-specific local authority flag
- isElectionAuthority - Election-related local authority

### Location-Based Ranking (doj)
- brickAndMortarStrengthScore - Physical business presence authority
- geotopicality - Page-query geographic relevance alignment
- NavBoostLocationSlice - Click data segmented by geography
- NavBoostDeviceSlice - Click data segmented by device type
- Local click patterns differ from national patterns
- Same URL can rank differently in different locations
- Local business as top mobile intent (UPX1087, Mobile vs Desktop Aug 2014): Local business was the top mobile search intent category in 2014. Mobile intents diverge significantly from desktop, with local queries dominating mobile search behavior

### 21 Patents (patent)
- Venice (2012) - Local results in organic SERPs
- Pigeon (2014) - Distance and prominence as factors
- Possum (2017) - Business address diversification
- Google Business Profile signals (hours, reviews, photos)
- Proximity: distance between user and business
- Prominence: how well-known the business is (links, reviews, mentions)
- Title-based local ranking - Patent US8122013 (Qian, Luk, An, filed 2006): Ranks local business listings by web popularity (webscore). webscore = log10(raw_webscore) * 0.2, where raw_webscore = web document count for business title. Short titles searched as [title AND city], long titles (>=3 words) searched alone. Duplicate business names: webscore divided by number of duplicates. Location Prominence Score combines webscore with authority page ranking, address reference count, reviews, and scraped page references
- US9858291: Detection of related local entities; US20080172374: Presentation of Local Results; US9792330: Identifying local experts for local search
- US7100034: System for selecting another processor to be the boot strap processor when the default boot strap...; US6647269: Method and system for analyzing advertisements delivered to a mobile unit; US8392394: Merging search results
- US7483881: Determining unambiguous geographic references; US8200694: Identification of implicitly local queries; US8407211: Determining relevance scores for locations
- US7017109: E-service to manage contact information and signature ECards; US20090303036: Machine-readable representation of geographic information; US7373246: Using boundaries associated with a map view for business location searching
- US6393459: Multicomputer with distributed directory and operating system; US7216123: Methods for ranking nodes in large directed graphs; US6889213: E-service to manage contact information with privacy levels
- US11120090: On-device query rewriting; US6691158: E-service to manage contact information and track contact location; US7974994: Sensitive webpage content detection
- US6633311: E-service to manage and export contact information; US8341156: System and method for identifying erroneous business listings

### How Local Ranking Affects Your SEO (exploit)
- Build genuine local engagement for NavBoostLocationSlice: Click data is sliced by geographic location, meaning the same URL ranks differently in different cities based on local click patterns. To rank in a specific metro, you need clicks and long-dwell engagement from users IN that location. Run geo-targeted campaigns (local ads, community events, local PR) that drive real local users to your site, not just national traffic.
- Maximize brickAndMortarStrengthScore with a complete GBP: This DOJ-confirmed signal scores physical business presence. Pure online businesses get zero local boost. Complete your Google Business Profile with verified address, accurate hours, photos of your location, and respond to reviews. NAP (Name, Address, Phone) consistency across citations directly feeds this score.
- Avoid locationIdentity demotion from fake local pages: Programmatic location pages targeting cities you have no presence in trigger locationIdentity demotion. Instead of creating 500 city pages with templated content, focus on genuine local content for areas where you have physical presence, local customers, or verifiable local expertise. Quality over geographic breadth.
- Optimize mobile experience for local queries: NavBoostDeviceSlice weights mobile engagement ~1.5x for local queries, and approximately 60% of local searches happen on mobile. Mobile page speed, tap target sizing, and click-to-call functionality directly impact whether local mobile users generate the long-click signals that feed your local rankings.
- Create genuine geotopicality signals in content: geotopicality measures alignment between page content and query location. Mention specific neighborhoods, landmarks, cross-streets, and local entities naturally in your content. Use LocalBusiness structured data with precise coordinates. Generic content with a city name appended does not build geotopicality; content that demonstrates local knowledge does.
- Earn localAuthorityScore through local link building: Local authority is measured separately from general domain authority. Links from local newspapers, chambers of commerce, local business directories, community organizations, and city government sites build localAuthorityScore specifically. A link from your city's newspaper is worth more for local rankings than a link from a national publication.

### brickAndMortarStrengthScore (DOJ)
Scores physical business locations for local search. Part of LocalWWWInfo module. Likely draws from Google Business Profile data.
Formula: brickAndMortarStrength(biz) = GBPVerified(
profileCompleteness, NAPConsistency, citationCount
)  // 0.0-1.0, LocalWWWInfo module
if !physicalAddress: score = 0
// pure online businesses get no local boost
Patents: US8700580 (local search ranking), US9009177 (business listing quality)
Affects: Affected by: having verified Google Business Profile, NAP consistency, local citations.

### geotopicality (DOJ)
Measures alignment between page content and query location. Part of LocalWWWInfo module. Cross-references content topics with geographic signals.
Formula: geotopicality(page, query) = geoAlign(
contentGeoEntities, queryLocation, structuredGeoData
)  // 0.0-1.0, LocalWWWInfo
// pages targeting non-matching areas trigger locationIdentity demotion
Patents: US8700580 (local search ranking)
Affects: Affected by: including local geographic references in content. Location-specific content, local structured data.

### NavBoostLocationSlice (DOJ)
NavBoost slices click data by geographic location. Users in different areas generate different click patterns, leading to location-specific ranking adjustments.
Formula: NavBoostLocationSlice(url, geo) = aggregate(
clicks[geo], longClicks[geo], lastLongClick[geo]
)  // per-geography click aggregation
// same URL ranks differently in NYC vs LA based on local CTR
Affects: Affected by: local user engagement. If users in your area click and stay on your site, your local rankings improve.

### NavBoostDeviceSlice (DOJ)
NavBoost slices click data by device type (mobile/desktop/tablet). Mobile users may behave differently than desktop for local queries.
Formula: NavBoostDeviceSlice(url, device) = aggregate(
clicks[device], dwellTime[device], bounceRate[device]
)  // mobile vs desktop vs tablet
if device == mobile && localQuery: weight *= 1.5
// ~60% of local queries are mobile
Affects: Affected by: mobile optimization for local searches. Most local queries are mobile.

### Title-Based Webscore (Patent US8122013 (Qian, Luk, An, 2006))
Ranks local business listings by web popularity derived from business title. Searches the web for the business name and counts matching documents as raw webscore. Short titles (<3 words) searched with city qualifier, long titles searched alone. Scaled logarithmically with 0.2 weight. Duplicate business names share webscore equally. Location Prominence Score combines webscore with authority page ranking, address reference count, number of reviews, and scraped page references.
Formula: webscore(listing) =
  log10(raw_webscore) × 0.2
// raw_webscore = web document count for title
// short titles: search [title AND city]
// long titles (>=3 words): search [title]

if duplicates: webscore /= num_duplicates

LocationProminence = f(
  authority_rank, page_rank,
  address_refs, scraped_refs,
  reviews, webscore
)
Patent: US8122013 (Title based local search ranking, 2006)
Affects: Affected by: business name web presence, title length (short vs long), number of web references, duplicate business handling, review count, address citation frequency.

## Pipeline
- **Query Detection**: Local intent identification, geotopicality
- **Location Signals**: geoFeatureType, geoLocationQuality, localityScore
- **Business Verification**: brickAndMortarStrengthScore, physical presence
- **NavBoost Location**: Click data sliced by user geography
- **Local Ranking**: localAuthorityScore + proximity + prominence

## Timeline
- 2004: Local Search Patents - Google files patents on geographic relevance in search. Location-aware ranking begins.
- 2006: Title-Based Local Ranking (US8122013) - Google patents webscore: log10(raw_webscore) * 0.2 for local business listings. Short titles searched with city qualifier, long titles alone. Duplicate names share webscore. Location Prominence Score combines webscore with authority, reviews, and citations.
- 2012: Venice Update - Local results integrated into organic SERPs. Location-specific queries receive localized results.
- 2014: Pigeon Update - Major local algorithm update. Distance and location prominence become stronger ranking factors.
- 2017: Possum Update - Diversifies local results by business address. Filters duplicate businesses at same location.
- 2014: Local Business: Top Mobile Intent (UPX1087) - Mobile vs Desktop Ranking analysis (Aug 2014): Local business was the top mobile search intent category. Mobile intents diverge significantly from desktop. Mobile search 'already incorporated into many people's life cycle.' Mobile traffic exceeded desktop on weekends.
- 2024: API Leak: Geo Signals - geoFeatureType, geoLocationQuality, localAuthorityScore, localityScore, locationIdentity confirmed.
- 2024: DOJ Trial: NavBoost Location - NavBoostLocationSlice segments click data by geography. brickAndMortarStrengthScore measures physical presence.

## Contradictions
- Public (Google Public): Local ranking is primarily based on relevance, distance, and prominence.
  Internal (API Leak + DOJ): NavBoostLocationSlice means click data is geographically segmented. A page popular in NYC may rank differently in LA. Click-based ranking is location-specific, not just distance-based.
- Public (Google Public): Any business can rank locally regardless of size.
  Internal (DOJ Trial): brickAndMortarStrengthScore explicitly measures physical business presence. Pure online businesses without physical locations are disadvantaged for local queries.
- Public (Google Public): Location pages are fine as long as they provide unique value.
  Internal (API Leak): locationIdentity demotes pages targeting non-matching geographic areas. Programmatic location pages without genuine local presence trigger demotion signals.
