# Tangram: Google's SERP Assembly Engine

## Key Insight
Tangram is the system that determines what SERP features you see for any given query. It doesn't rank pages; it assembles the final layout from pre-ranked results. This means two things for SEO: (1) Your page can rank #1 in organic results but still appear below a Knowledge Panel, featured snippet, People Also Ask box, and video carousel, (2) Tangram uses Instant Glue with ~10-minute latency to dynamically adjust layouts for time-sensitive queries, meaning SERP features can change rapidly during breaking news events. The system evolved from an internal project called Tetris, reflecting its role in 'fitting' different result types into the available SERP space.

### DOJ Trial: Sworn Testimony (doj)
- Tangram (SERPAssembly module): SERP assembly and layout system evolved from 'Tetris'. Created by HJ Kim (PXR0356, Feb 2025). Arranges blue links, universal features, and ads for optimal space and UX. Confirmed via sworn testimony.
- Tetris (SERPAssembly module): Original codename for Tangram. HJ Kim named it 'fka Tetris'. Promotes fresh content and demotes stale results for time-sensitive queries. Confirmed as deprecated name.
- Tangram is the final stage in the SuperRoot pipeline: Query Understanding > Mustang > Ascorer > Twiddlers > Tangram > SERP output
- No leak signals or patents exist for Tangram; it's known exclusively through DOJ evidence, suggesting it's a relatively recent or highly protected system

### Related Leak Evidence (leak)
- No direct Tangram signals in API leak, but related systems are well-documented:
- SnippetBrain: ML-powered snippet generation. Selects best text passage for search result display. Output feeds into Tangram for layout decisions.
- SuperRoot: Orchestration layer that routes queries through the pipeline. Tangram is the final stage before SuperRoot delivers the SERP.
- InstantGlue: Real-time Glue variant with ~10-minute latency. Feeds freshness signals to Tangram for time-sensitive query layout adjustments.

### Related Patent Context (patent)
- Tangram itself has 0 mapped patents, unusual for a core Google system. This may indicate it's newer, or its patents are classified under SuperRoot/Glue.
- Glue system: 77 patents covering user interaction aggregation and SERP feature scoring. These likely inform Tangram's layout decisions.
- SuperRoot: Patent evidence for pipeline orchestration that includes SERP assembly as the final step.
- SnippetBrain: Patents for ML-based snippet selection that feeds directly into Tangram's output.
- AI-generated content pages - Patent US12536233 (Google, filed 2025): System generates AI-tailored landing pages displayed within SERPs. Calculates landing page score (conversion rate, bounce rate, CTR, design quality). When score exceeds threshold, inserts navigation link to AI-generated alternative with personalized headlines, product feeds, chatbot integration, and suggested filters. Google directly competes with organization landing pages through SERP presentation control
- Generative summaries for search results - Patent US11769017 (Google, filed 2023): LLM-generated natural language summaries rendered directly in SERP. Processes query + search result documents to prevent hallucinations. Dynamic revision: monitors user interaction duration, triggers revised summaries using fine-tuned LLMs when engagement threshold exceeded. Content verification generates selectable attribution links to source documents. Foundation for AI Overviews

### How SERP Assembly Affects Your Traffic (exploit)
- Position != visibility: Ranking #1 organically but appearing below 3 SERP features means your result may be 'below the fold.' Tangram controls this layout.
- Feature eligibility matters: Getting into featured snippets, Knowledge Panels, or video carousels gives you Tangram-level visibility. Optimize for SERP features, not just rankings.
- Time-sensitive queries shift layouts: During breaking news, Instant Glue feeds Tangram to promote fresh content and suppress stale results within 10 minutes. Freshness matters for trending topics.
- Query classification drives features: Tangram uses query classification to decide which features to show. Navigational queries get site links; informational queries get PAA and snippets; transactional queries get shopping results.
- AI Overviews (MAGIT/Gemini): Tangram integrates AI-generated overviews into the SERP. These can push organic results significantly lower.
- Mobile vs desktop layouts differ: Tangram assembles different layouts per device. Mobile SERPs are more feature-heavy, reducing organic click-through rates.
- The SERP is modular: Each feature type (blue links, images, videos, maps, news) is a module that Tangram fits into available space. Your content should be eligible for multiple module types.

### SERP Assembly (DOJ)
Tangram receives ranked results from the Ascorer/Twiddlers pipeline and query classification from Query Understanding. It then assembles the final SERP by selecting which modules (blue links, snippets, Knowledge Panel, PAA, images, videos, ads, AI Overview) to display and in what arrangement. Space allocation is optimized for user engagement and query satisfaction.
Formula: SERP = Tangram(
  ranked_results,  // from Ascorer/Twiddlers
  query_class,     // navigational/informational/transactional
  available_modules // snippets, KP, PAA, images, videos
)
// Output: arranged layout with space allocation
// AI Overview (MAGIT/Gemini) integrated here
Affects: Affected by: query intent classification, available SERP feature candidates, device type, freshness signals from Instant Glue, user location

### Instant Glue Freshness Feed (DOJ + Leak)
Instant Glue operates on a 24-hour data window with approximately 10-minute latency, feeding real-time user engagement signals to Tangram. For breaking news or trending queries, this allows Tangram to rapidly adjust the SERP layout: promoting fresh content, adding news carousels, and suppressing stale results without waiting for the standard 13-month NavBoost cycle.
Formula: InstantGlue(query) =
  aggregate(
    clicks_24h,
    impressions_24h,
    engagement_24h
  )
// Latency: ~10 minutes
// Window: 24 hours
// Feeds Tangram for breaking news SERP adjustments
Affects: Affected by: real-time click patterns, trending query detection, breaking news events, content freshness

### Landing Page Score (AI Page Decision) (Patent US12536233 (Google, 2025))
Determines whether to generate an AI alternative to an organization's landing page directly in the SERP. Calculates a landing page score from multiple metrics: conversion rate, bounce rate, click-through rate, and qualitative factors (page design quality, content quality). When score exceeds threshold, Tangram inserts a navigation link to the AI-generated page. The AI page includes personalized headlines, product feeds, suggested filters, sitelinks, and optionally an AI chatbot. This represents Google's mechanism to compete directly with publisher landing pages at the SERP presentation layer.
Formula: LandingPageScore(page) = f(
  conversion_rate,
  bounce_rate,
  CTR,
  design_quality,
  content_quality
)
if score > threshold:
  insert AI-generated alternative
// AI page: personalized headlines, product feeds,
// chatbot, filters, previous query context
Patent: US12536233 (AI-generated content page, 2025)
Affects: Affected by: landing page usability, conversion rate, bounce rate, design quality. Organizations with poor landing pages may see Google's AI alternative promoted instead.

### Generative Summary with Dynamic Revision (Patent US11769017 (Google, 2023))
LLM generates natural language summaries for search queries, rendered directly in SERP as AI Overviews. Processes query content plus additional content from search result documents to prevent hallucinations and stale information. Monitors user interaction with search result documents; when viewing duration exceeds threshold, generates revised summary using fine-tuned LLMs that assume document familiarity. Verified summary portions get selectable attribution links to source documents.
Formula: Summary(query) = LLM(
  query,
  search_results,
  additional_content
)
// prevents hallucination via grounding

if userDwell(doc) > threshold:
  RevisedSummary = FineTunedLLM(
    original + doc_content
  )
// attribution links for verified portions
Patent: US11769017 (Generative summaries for search results, 2023)
Affects: Affected by: search result document quality, query specificity, user engagement duration, LLM grounding quality. Foundation for AI Overviews in SERP.

## Pipeline
- **Query Understanding**: Query classified by intent (navigational, informational, transactional), topic, language, location. Determines which SERP features are candidates.
- **Mustang Retrieval**: Initial document retrieval from the index using T* (Topicality). Thousands of candidate documents selected.
- **Ascorer + Twiddlers**: Candidates re-ranked using ~1,700 signals. NavBoost engagement data applied. Final ranking produced.
- **Feature Candidates**: SnippetBrain selects best snippets. Knowledge Panel data loaded from Webref. Image/video candidates identified. PAA questions generated.
- **Tangram Assembly**: All ranked results and feature candidates assembled into final SERP layout. Space allocated by module type. Device-specific layouts generated.
- **Instant Glue Check**: For time-sensitive queries, Instant Glue feeds 24h engagement data with 10-min latency. Tangram adjusts layout: adds news carousels, promotes fresh content.
- **GWS Delivery**: Google Web Server renders the assembled SERP and delivers to the user's browser.

## Timeline
- ~2015: Tetris System Created - Internal project codenamed 'Tetris' built for SERP module assembly. Name reflects the game-like fitting of different result types into available space.
- ~2020: Renamed to Tangram - System renamed from Tetris to Tangram. The tangram puzzle metaphor better reflects assembling distinct shapes (modules) into a cohesive layout.
- 2024: DOJ Trial Confirmation - Tangram and Tetris both confirmed in sworn testimony as the SERP assembly system. Module: SERPAssembly. Integrated with Instant Glue for freshness.
- 2023: Generative Summaries Patent (US11769017) - Google patents LLM-generated SERP summaries with dynamic revision. Monitors user dwell time to trigger refined summaries via fine-tuned LLMs. Attribution links verify content against source documents. Foundation for AI Overviews.
- 2025: AI-Generated SERP Pages (US12536233) - Google patents AI-generated landing page alternatives displayed in SERPs. Landing page score (conversion, bounce, CTR, quality) determines when AI alternative is inserted. Includes personalized headlines, product feeds, chatbot, and suggested filters.
- 2025: AI Overview Integration - Tangram integrates AI-generated overviews (MAGIT/Gemini) into search results, fundamentally changing SERP layout for informational queries.
