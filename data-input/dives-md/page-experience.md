# Page Experience: UX as a Ranking Signal

## Key Insight
Core Web Vitals use real Chrome field data (mobileCwv, desktopCwv), not lab measurements. The adsDensityInterstitialViolationStrength is scored 0-1000, measuring exactly how aggressively a page uses ads and popups. clutterScore is a site-level penalty for distracting UI elements. The isSmartphoneOptimized signal is tri-state (not binary), meaning partially optimized sites get partial credit.

### UX Scoring Signals (leak)
- pageSpeedScore - Aggregated Core Web Vitals performance score
- mobileFriendlyScore - Mobile-friendliness for mobile-first indexing
- mobileInterstitials - Mobile popup/interstitial penalty signal
- desktopInterstitials - Desktop intrusive popup detection
- clutterScore - Site-level penalty for ads, popups, excessive UI

### Signals Confirmed (doj)
- mobileCwv - Core Web Vitals field data for mobile (LCP, INP, CLS)
- desktopCwv - Core Web Vitals field data for desktop devices
- clutterScore - Site-level penalization for distracting elements
- adsDensityInterstitialViolationStrength - Ad violation score, 0-1000 scale
- isSmartphoneOptimized - Tri-state mobile-friendliness classification
- badSslCertificate - Invalid SSL as negative trust indicator

### 7 Patents (patent)
- LCP - Largest Contentful Paint (loading performance)
- INP - Interaction to Next Paint (interactivity, replaced FID)
- CLS - Cumulative Layout Shift (visual stability)
- Field data from Chrome User Experience Report (CrUX)
- Mobile-first indexing: mobile version is primary for ranking
- HTTPS required since 2014, badSslCertificate penalizes invalid certs
- US20110142016: Ad hoc networking based on content and location; US8635106: System for targeting data to users on mobile devices; US20080035402: Mobile Device Suitable for Supporting Apparatus for Site Imaging While in Transit
- US7203684: Serving content-targeted ADS in e-mail, such as e-mail newsletters; US8271057: Condition-based activation, shut-down and management of applications of mobile devices; US8478519: Providing results to parameterless search queries
- US7742953: Adding information or functionality to a rendered document via association with an electronic cou...

### How Page Experience Affects Your SEO (exploit)
- Optimize for Chrome field data, not lab scores: mobileCwv and desktopCwv use real Chrome User Experience Report (CrUX) field data, not Lighthouse lab measurements. A perfect lab score means nothing if real users on slower devices experience poor performance. Monitor CrUX data in Search Console and PageSpeed Insights. Optimize for the 75th percentile of your actual user base, especially on mobile.
- Keep adsDensityInterstitialViolationStrength near zero: This 0-1000 scale precisely measures ad and popup aggressiveness. Even moderate scores create demotion. Avoid full-screen interstitials on mobile, limit above-the-fold ad density to under 30% of viewport, and never auto-play video ads. Follow Google's Better Ads Standards as a minimum baseline, since the internal scoring is more granular than the public guidelines suggest.
- Achieve fully-optimized status in the tri-state mobile classifier: isSmartphoneOptimized is not binary but tri-state (optimized, partial, not optimized). Partially optimized sites receive partial credit, not full. Ensure responsive design with proper viewport meta tags, readable text without zooming (16px+ base font), tap targets at least 48px apart, and no horizontal scrolling. Test on actual mid-range Android devices, not just iPhone.
- Minimize clutterScore at the site level: clutterScore is a site-level penalty, not per-page. If some pages on your site have excessive ads, popups, or distracting elements, the penalty affects your entire domain's page experience score. Audit all page templates including error pages, category pages, and archive pages for clutter, not just your primary content pages.
- Target LCP under 2.5s by optimizing the largest element: LCP measures when the largest contentful element renders. Identify your LCP element (usually a hero image or heading text) and optimize specifically for it. Preload hero images, use next-gen formats (WebP/AVIF), set explicit width/height to avoid layout recalculation, and eliminate render-blocking resources that delay the LCP element.
- Fix CLS by reserving space for dynamic content: Cumulative Layout Shift (CLS < 0.1 threshold) penalizes visual instability. Common causes: ad slots without reserved dimensions, lazy-loaded images without aspect-ratio attributes, dynamically injected content above the fold, and web fonts causing text reflow. Set explicit dimensions on all media elements and use font-display: optional to prevent layout shifts.
- Yandex cross-reference: ad density is the single strongest negative signal: Yandex's FI_ADV has a weight of -0.251, the heaviest negative factor in their entire 17,854-factor ranking system. This cross-validates Google's clutterScore and adsDensityInterstitialViolationStrength as among the most impactful ranking penalties. Yandex also directly penalizes crawl depth (URL distance from homepage) and orphan pages (no internal links), suggesting these factors influence Google's page experience evaluation even if not explicitly documented as separate signals.

### mobileCwv / desktopCwv (DOJ: Core Web Vitals field data)
Field data from real Chrome users. Measures LCP (Largest Contentful Paint), FID/INP (Interaction to Next Paint), CLS (Cumulative Layout Shift). Separate scores for mobile and desktop.
Formula: CWV(url, device) = CrUX_field_data(
LCP < 2.5s, INP < 200ms, CLS < 0.1
)  // separate mobile and desktop scores
// real Chrome user data, not synthetic tests
Patents: US8645362 (resource load times as ranking signal)
Affects: Affected by: optimizing page speed (LCP < 2.5s), interactivity (INP < 200ms), visual stability (CLS < 0.1). Use Chrome UX Report data.

### clutterScore (DOJ: site-level penalization for distracting elements)
Site-level penalty for distracting elements. Evaluates ad density, pop-ups, interstitials, and other elements that degrade user experience.
Formula: clutterScore(site) = evaluate(
ad_density, popups, interstitials, distractions
)  // site-level penalty, not per-page
// higher clutter = stronger demotion
Patents: US8645362 (resource load times and trust)
Affects: Affected by: reducing intrusive ads, pop-ups, interstitials. Clean layouts score better. Especially important on mobile.

### adsDensityInterstitialViolationStrength (DOJ: scaled 0-1000 ad density violations)
Scaled 0-1000 score. Measures strength of ad density and interstitial policy violations specifically on mobile pages. Higher = worse.
Formula: adsDensityViolation(page) = score(
mobile_ad_density, interstitial_violations
)  // range: 0 (clean) - 1000 (severe)
// mobile-specific, follows Better Ads Standards
Affects: Affected by: reducing mobile ad density. Avoid full-screen interstitials. Follow Google's Better Ads Standards.

### isSmartphoneOptimized (DOJ: tri-state mobile-friendliness classification)
Tri-state classification for smartphone optimization. Not just pass/fail - three levels of mobile-friendliness assessment.
Formula: isSmartphoneOptimized(page) = classify(
responsive, tap_targets, viewport, text_size
)  // tri-state: optimized | partial | not_optimized
// NOT binary (contradicts public statements)
Patents: US8645362 (resource load times as ranking signal)
Affects: Affected by: responsive design, readable text without zooming, adequate tap targets, no horizontal scrolling on mobile.

## Pipeline
- **Chrome Field Data**: Real user CWV metrics (LCP, INP, CLS)
- **Mobile Analysis**: isSmartphoneOptimized (tri-state), mobileFriendlyScore
- **UX Penalties**: clutterScore, interstitials, adsDensity (0-1000)
- **Trust Checks**: HTTPS validation, badSslCertificate
- **Page Experience Score**: Combined UX quality signal for ranking

## Timeline
- 2010: Mobile-Friendly Signals - Google begins factoring mobile usability into rankings. Mobile-first thinking starts.
- 2015: Mobilegeddon Update - Mobile-friendliness becomes a ranking factor. Pages not optimized for mobile demoted in mobile search.
- 2018: Speed Update - Page speed becomes a ranking factor for mobile searches. Slow sites demoted.
- 2020: Core Web Vitals Announced - Google introduces LCP, FID, CLS as standardized UX metrics. Planned as ranking signals.
- 2021: Page Experience Update - Core Web Vitals officially become ranking signals. Combined with mobile-friendliness, HTTPS, no intrusive interstitials.
- 2023: Yandex Leak Cross-Reference - Yandex's FI_ADV (advertising presence, weight: -0.251) is the heaviest single negative factor in their entire ranking system, validating ad density as a dominant penalty. FI_IS_MOBILE_BEAUTY_HOST scores mobile optimization at the host level. Crawl depth is weighted directly: pages with more URL slashes (deeper in site hierarchy) are penalized. Orphan pages (no internal links) receive specific negative signals. Embedded maps are a positive signal when contextually relevant.
- 2024: API Leak: UX Signals - clutterScore, pageSpeedScore, mobileInterstitials, desktopInterstitials confirmed. Ad density penalties revealed.
- 2024: DOJ Trial: CWV Field Data - mobileCwv and desktopCwv use real Chrome field data. adsDensityInterstitialViolationStrength scored 0-1000.

## Contradictions
- Public (Google Public): Core Web Vitals is a minor ranking factor. Content relevance matters much more.
  Internal (API Leak + DOJ): Dedicated mobileCwv and desktopCwv signals use real Chrome field data. adsDensityInterstitialViolationStrength is a precise 0-1000 scale penalty. These are concrete, measured ranking inputs.
- Public (Google Public): Mobile-friendliness is a simple binary signal.
  Internal (DOJ Trial): isSmartphoneOptimized is tri-state, not binary. Sites can be fully optimized, partially optimized, or not optimized, each with different ranking treatment.
- Public (Google Public): We don't penalize sites for showing ads.
  Internal (API Leak + DOJ): adsDensityInterstitialViolationStrength explicitly penalizes ad density and popups on a 0-1000 scale. clutterScore penalizes distracting page elements at site level.
