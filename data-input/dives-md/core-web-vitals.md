# Core Web Vitals: Chrome Field Data as Ranking Input

## Key Insight
Google measures CWV using real Chrome browser field data, not synthetic lab tests. The pageSpeedScore signal aggregates CWV performance into a single ranking input. Chrome's chromeInTotal signal (confirmed in NavBoost as a ranking signal) demonstrates that Chrome browser data feeds multiple ranking systems simultaneously. CrUX collects anonymized field metrics from opted-in Chrome users, meaning sites with low Chrome traffic may have insufficient CWV data for reliable scoring. Despite Google's public emphasis on CWV, internal evidence positions it as a tiebreaker between pages of similar relevance and authority, not a primary ranking driver.

### API Leak Signals (leak)
- pageSpeedScore - Aggregated Core Web Vitals and page speed performance score, combining LCP, INP, and CLS into a single ranking input
- CWV field data feeds into the broader Page Experience system alongside clutterScore, mobileInterstitials, and mobileFriendlyScore
- Chrome browser data (chromeInTotal confirmed in NavBoost) provides the raw user experience metrics that underpin CWV measurement
- No separate CWV-specific leak signals were found beyond pageSpeedScore. Individual LCP, INP, CLS values are likely computed internally but exposed as a composite score

### 2 Patents (patent)
- US9418171: Acceleration of rendering of web-based content - Methods for optimizing how web pages are rendered, directly related to how Google measures LCP and rendering performance at scale
- US8645362: Using resource load times in ranking - Establishes resource load time as a ranking signal, the foundational patent for using page speed metrics (including CWV predecessors) in search ranking

### CWV Optimization Impact (exploit)
- Optimize for Chrome field data (CrUX), not Lighthouse lab scores: pageSpeedScore uses real user data from Chrome, not synthetic benchmarks. A perfect Lighthouse score means nothing if real Chrome users on mid-range Android devices experience slow LCP. Monitor CrUX data via Search Console and PageSpeed Insights. Target the 75th percentile of real user experiences.
- LCP under 2.5s requires optimizing the single largest element: Identify your LCP element (usually hero image or heading). Preload it, use WebP/AVIF, set explicit dimensions, eliminate render-blocking CSS/JS before it. Server response time (TTFB) is the floor for LCP; no front-end optimization can compensate for a 3-second server response.
- INP under 200ms requires minimizing main thread blocking: Interaction to Next Paint replaced FID in March 2024. Unlike FID (first input only), INP measures the worst interaction across the entire page lifecycle. Break up long JavaScript tasks, defer non-critical JS, use web workers for heavy computation. Test with real interactions, not just page load.
- CLS under 0.1 requires reserving space for all dynamic content: Set explicit width/height on images and videos, reserve ad slot dimensions, use font-display: optional to prevent text reflow. CLS is measured across the entire page session, not just initial load. Late-loading ads and lazy images are the most common offenders.
- CWV is a tiebreaker, not a primary factor: Internal evidence shows CWV operates within the Page Experience signal, which itself is secondary to NavBoost, content quality, and link authority. Fixing CWV will not rescue a page with poor content or no backlinks. But between two equally relevant pages, CWV can determine which ranks higher.
- Low-traffic sites may lack sufficient CrUX data: CrUX requires a minimum threshold of Chrome user visits to report field data. Sites below this threshold may not have a pageSpeedScore at all. Google likely falls back to origin-level (domain-wide) CrUX data or page-level estimates for these URLs.

### System Context (doj)
- chromeInTotal confirmed as a ranking signal in NavBoost (DOJ trial). Chrome browser data feeds both click signals and CWV field metrics, making Chrome the primary data source for user experience measurement
- No CWV-specific DOJ exhibits exist. CWV evidence is derived from the broader Page Experience and Chrome data context established in the trial
- DOJ trial confirmed that user experience signals (including those from Chrome) are real ranking inputs, contradicting earlier Google claims that such signals were too noisy to use directly
- NavBoost's reliance on Chrome data (chromeInTotal, RealTimeBoost) establishes that Google's Chrome browser serves as a large-scale telemetry platform feeding multiple ranking subsystems including CWV

### Calculations
- pageSpeedScore (CWV Composite): Combines the three Core Web Vitals metrics into a single performance score. Uses Chrome field data (CrUX) at the 75th percentile. Each metric is evaluated against its threshold: LCP < 2.5s (good), INP < 200ms (good), CLS < 0.1 (good). Pages passing all three thresholds receive the strongest signal. Affects: Affected by: server response time (TTFB), render-blocking resources, image optimization, JavaScript execution time, layout stability. Feeds into Page Experience system.
- LCP Rendering Path: Largest Contentful Paint measures when the largest visible element finishes rendering. Google's rendering pipeline (US9418171) accelerates this measurement. The LCP element is typically a hero image, video poster, or large text block. Server response time sets the floor; no front-end optimization can overcome slow TTFB. Affects: Affected by: TTFB, image format/compression, preloading critical resources, eliminating render-blocking CSS/JS, CDN usage.
- CLS Stability Score: Cumulative Layout Shift measures unexpected visual movement during page load and interaction. Calculated as impact fraction (viewport area shifted) multiplied by distance fraction (how far elements moved). Measured across the entire session using a windowed approach (max session window of 5 seconds, capped at 1 second gap). Affects: Affected by: explicit image/video dimensions, reserved ad slot sizes, font-display strategy, avoiding dynamic content injection above the fold.

### Core Web Vitals Measurement and Ranking Pipeline
- Chrome Field Collection: Opted-in Chrome users generate anonymized performance metrics (LCP, INP, CLS) during real browsing sessions
- CrUX Aggregation: Chrome User Experience Report aggregates field data at URL and origin level, computing 75th percentile values
- Threshold Evaluation: Each CWV metric evaluated against thresholds: LCP < 2.5s, INP < 200ms, CLS < 0.1 (good/needs improvement/poor)
- pageSpeedScore Computation: Individual CWV metrics combined into aggregated pageSpeedScore for the URL or origin
- Page Experience Integration: pageSpeedScore combined with mobileFriendlyScore, clutterScore, HTTPS status, interstitial penalties
- Tiebreaker Application: Page Experience signal applied as tiebreaker between pages with similar relevance, authority, and content quality scores
- Device-Specific Scoring: Separate mobile and desktop CWV scores (mobileCwv, desktopCwv) applied to device-appropriate search results

### Timeline
- 2012 (patent): Resource Load Times Patent (US8645362) - Google patents the use of resource load times as a ranking signal. Foundational patent for speed-based ranking, predating CWV by 8 years.
- 2015 (patent): Rendering Acceleration Patent (US9418171) - Patent for accelerating rendering of web-based content. Enables large-scale measurement of rendering performance metrics like LCP.
- 2018 (patent): Speed Update - Page speed becomes a mobile ranking factor. Google publicly acknowledges speed affects rankings, initially only for the slowest sites.
- 2020 (patent): Core Web Vitals Announced - Google introduces LCP, FID, CLS as standardized metrics. CrUX begins providing public field data. Announcement signals future ranking integration.
- 2021 (patent): Page Experience Update Rollout - CWV officially becomes a ranking signal as part of Page Experience. Rolled out gradually June-August 2021 for mobile, February 2022 for desktop.
- 2024 (patent): INP Replaces FID - Interaction to Next Paint replaces First Input Delay as the responsiveness metric (March 2024). INP measures all interactions, not just the first.
- 2024 (leak): API Leak: pageSpeedScore Confirmed - API leak reveals pageSpeedScore as the aggregated CWV performance signal. Confirms Chrome field data (not lab tests) as the data source for speed-based ranking.

### Contradictions
- Public (Google Public Statements): Core Web Vitals is an important ranking signal. Sites should prioritize CWV optimization for better rankings. vs Internal (API Leak + DOJ Trial Context): CWV operates as a tiebreaker within the Page Experience signal, which is itself secondary to NavBoost (91% accuracy improvement), content quality, and link authority. pageSpeedScore is one input among hundreds.
- Public (Google Public Statements): We use a variety of signals to measure page experience, with CWV being the most important component. vs Internal (API Leak Evidence): The API leak shows adsDensityInterstitialViolationStrength (0-1000 scale) and clutterScore as potentially more impactful Page Experience sub-signals. A page with perfect CWV but aggressive ads still gets demoted.
- Public (Google Search Central): PageSpeed Insights and Lighthouse provide actionable CWV guidance. Lab and field data are both useful. vs Internal (Chrome Data Pipeline): Only Chrome field data (CrUX) feeds into ranking. Lab scores from Lighthouse have zero direct ranking impact. Sites with insufficient Chrome traffic may lack field data entirely, receiving no CWV ranking benefit regardless of lab performance.

### Overview
Core Web Vitals (LCP, INP, CLS) are Google's standardized user experience metrics, measured via real Chrome field data from the Chrome User Experience Report (CrUX). The API leak confirmed pageSpeedScore as the aggregated performance signal. Patents reveal Google has invested in rendering acceleration and resource load time measurement since the early 2010s. CWV operates as a tiebreaker signal within the broader Page Experience system, not as a primary ranking factor like NavBoost or content quality.
