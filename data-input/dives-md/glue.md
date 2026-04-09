# Glue: The Super Query Log

## Key Insight
Glue is NavBoost for everything that isn't a blue link. It captures query metadata, SERP layout, click patterns, mouseover events, dwell time per result, scroll depth, and query reformulations. Instant Glue provides real-time signals with ~10 minute latency on 24-hour data windows, used for breaking news. The system learns which SERP features satisfy user intent for specific query types, directly controlling feature placement.

### Signals,  Core (doj)
- Glue - Super query log extending NavBoost to all SERP features (sworn testimony)
- Instant Glue - Real-time pipeline with ~10 min latency on 24h data windows
- GlueSERPInteractions - Full interaction logging: clicks, hovers, scroll, dwell, reformulations
- Captures SERP layout configuration for each query
- Learns which features satisfy intent per query type
- Feeds Tangram SERP assembly system for feature placement

### Signals Confirmed (leak)
- Glue - Universal search results assembly using user behavior signals
- socialEngagementSignal - Social media engagement measurement for result integration
- GlueVoterTokenBitmapMessage - User de-duplication system for all SERP features. Uses 4 uint64s as a 256-bit bitmap to aggregate distinct voter tokens in the Glue pipeline. Extends NavBoost's voterTokenCount concept beyond blue links to Knowledge Panels, video carousels, image packs, People Also Ask, and AI Overviews. Stores a single uint64 voterToken when only one user contributed, or subRange (string list) bitmap components for multiple tokens. Enables approximate distinct-user counting across all SERP feature types
- Glue module handles SERP feature decisions based on interaction data
- Social engagement directly influences search result treatment
- Connected to SocialGraphApiProto for social data integration

### Related Patents (patent)
- Universal Search blending algorithms for mixed result types
- User interaction heatmaps for SERP layout optimization
- Feature satisfaction measurement per query category
- Real-time trending signal detection for news and events
- Query reformulation tracking to measure result satisfaction
- Cross-feature cannibalization analysis for optimal placement

### How Glue Affects Your SEO (exploit)
- Optimize for all SERP interactions, not just clicks: Glue tracks mouseovers, scroll depth, dwell time per result, and query reformulations. A result that gets hovered but not clicked sends a different signal than one that is skipped entirely. Compelling meta descriptions and rich snippets that attract attention (even without clicks) build positive Glue signals for your listing.
- Reduce query reformulations after users visit your page: When a user clicks your result and then returns to Google to reformulate their query, Glue records this as a dissatisfaction signal. Ensure your content fully answers the query so users do not need to search again. Cover the topic comprehensively and address common follow-up questions on the same page.
- Maximize dwell time through engaging content structure: GlueSERPInteractions logs dwell time per result. Longer engagement signals content satisfaction. Use clear formatting, progressive disclosure, visual elements, and compelling writing to keep users engaged. Front-load the answer to prevent immediate bounces, then provide depth for users who want more.
- Leverage chromeScrollingIntensity by creating scroll-worthy content: Chrome measures scroll depth, scroll speed, and pause patterns. High scroll depth combined with reading pauses (not rapid scrolling) signals engaged reading. Structure long-form content with clear sections, embedded visuals, and progressive value to encourage deep scrolling with genuine reading behavior.
- Target SERP features that Glue data favors for your queries: Glue learns which SERP features satisfy user intent per query type. If your target queries consistently show featured snippets, video carousels, or image packs, optimize for those specific features. The Tangram system uses Glue data to assemble SERPs, so matching the format users prefer for that query type increases your chances of appearing.
- Build social engagement signals for Glue integration: The API leak confirmed socialEngagementSignal as a Glue system signal connected to SocialGraphApiProto. Despite Google publicly denying social signals matter, social media engagement demonstrably feeds into SERP feature decisions. Content with strong social engagement gets additional consideration in Glue's feature placement logic.
- React quickly to trending queries for Instant Glue advantages: Instant Glue operates with 10-minute latency on 24-hour data windows and feeds into Tangram SERP assembly. For breaking news and trending topics, the first content to generate positive user engagement signals in the Instant Glue pipeline gets a real-time ranking advantage that compounds as more users interact with it.

### Glue (DOJ)
Internal super query log warehouse extending NavBoost to ALL SERP features (not just blue links). Captures query metadata, SERP layout, interaction signals (clicks, hovers, dwell time), query interpretation data.
Formula: Glue(query) = logWarehouse(
  clicks, hovers, dwellTime, scrollDepth, reformulations
)  // extends NavBoost to ALL SERP features
// not just blue links: images, videos, knowledge panels, featured snippets
Patents: US8661029 (click signals), US8874555 (search result modification)
Affects: Affected by: all SERP interactions matter, not just clicks. Hovers, scroll depth, time on page all feed into Glue.

### Instant Glue (DOJ)
Real-time pipeline variant with ~10-minute latency on 24-hour data windows. Used for breaking news and trending queries. Feeds into Tangram for SERP assembly.
Formula: InstantGlue(query, t) = realTimeAggregate(
  interactions[t-24h..t]
)  // ~10 min latency, 24h sliding window
output → Tangram(SERP assembly)
// breaking news and trending queries use this path
Affects: Affected by: speed on trending topics. Real-time engagement signals update rankings within minutes.

### GlueSERPInteractions (DOJ)
Logs everything: query text, user location/device, SERP layout, click patterns, mouseover events, dwell time per result, scroll depth, query reformulations.
Formula: GlueSERPInteractions(session) = {
  queryText, location, device, serpLayout,
  clicks[], mouseover[], dwellTime[], scrollDepth, reformulations[]
}  // comprehensive per-session interaction log
Affects: Affected by: holistic user experience. Every interaction is tracked and feeds ranking.

### chromeScrollingIntensity (DOJ)
Chrome browser scrolling depth and intensity measurement. Content engagement signal from Chrome user data. Measures how much users actually read.
Formula: chromeScrollingIntensity(url) = measure(
  scrollDepth%, scrollSpeed, pausePatterns
)  // Chrome user data, content engagement proxy
// high scroll depth + pauses = engaged reading
Affects: Affected by: creating engaging content that users scroll through. Long-form content that holds attention.

## Pipeline
- **SERP Interaction**: Clicks, hovers, scroll depth, dwell time, reformulations
- **Glue Warehouse**: Super query log aggregating all SERP feature interactions
- **Instant Glue**: Real-time pipeline: ~10 min latency, 24h data windows
- **Feature Decisions**: Which SERP features appear for which query types
- **Tangram**: SERP assembly: blue links, features, ads layout

## Timeline
- 2008: Universal Search - Google launches blended search results mixing web, images, news, video. Need to track cross-feature interactions emerges.
- 2012: Search Plus Your World - Personalized results integration. User interaction tracking expanded beyond clicks to social signals.
- 2016: Glue System Built - Internal super query log warehouse created to extend NavBoost beyond blue links to all SERP features.
- 2020: Instant Glue Deployed - Real-time pipeline delivering fresh user signals with ~10 minute latency on 24-hour data windows.
- 2024: DOJ Trial: Glue Revealed - Pandu Nayak testified about Glue as super query log. Captures clicks, hovers, dwell time, scroll depth, SERP layout.
- 2024: API Leak: Social Signals - socialEngagementSignal in Glue system confirmed. Social media engagement feeds into SERP feature decisions.

## Contradictions
- Public (Google Public): "We don't track individual user behavior for ranking purposes."
  Internal (DOJ Trial): Glue captures comprehensive per-query interaction data: clicks, mouseovers, scroll depth, dwell time per result, query reformulations, device type, and user location. All fed back into ranking.
- Public (Google Public): "SERP features are determined by query type and content availability."
  Internal (DOJ Trial): Glue explicitly learns which features satisfy user intent for specific query types based on interaction patterns. Feature placement is driven by behavioral data, not just content matching.
- Public (Google Public): "We don't use social signals for ranking."
  Internal (API Leak): socialEngagementSignal is a confirmed leak signal in the Glue system, directly measuring social media engagement for search result integration.
