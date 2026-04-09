# Content Structure: How Google Reads Your Page

## Key Insight
onsiteProminence tracks how prominently a page is linked from your homepage and site structure, meaning your internal linking hierarchy directly affects ranking. titleQualityScore predicts title presentation quality, and Google WILL rewrite your title if it scores poorly. pageClassification categorizes pages by type (article, product, forum, etc.), affecting which ranking signals apply.

### Signals Confirmed (leak)
- onsiteProminence - Page prominence within site structure and internal linking
- titleQualityScore - Title presentation quality prediction for search results
- titleSize - Title length tracking (too long triggers rewrite)
- snippetPrefixCharCount - Character count for snippet extraction quality
- imageLayoutScore - Visual content placement and layout quality
- numImages - Image count on page (content richness signal)
- tagPageScore - Tag/category page quality scoring
- pageClassification - Page type categorization (article, product, forum)

### 1 Signal Confirmed (doj)
- avgTermWeight - Term importance weighting within document content
- Three Pillars - Body (document content) is the first pillar of ranking
- titlematchScore - Query-to-title relevance alignment measurement
- Passage-level indexing confirmed: individual sections rank independently
- Content segmentation separates main content from boilerplate

### 3 Patents (patent)
- Page segmentation and main content block identification (VIPS)
- Passage-level indexing and independent passage ranking
- Title quality evaluation and automatic title rewriting
- Featured snippet extraction from structured content
- Image-text relationship and layout quality scoring
- Internal link structure analysis for content hierarchy
- US6665837: Method for identifying related pages in a hyperlinked database; US20100049709: Generating Succinct Titles for Web URLs; US7716569: Framework for providing visual context to WWW hyperlinks

### How Content Structure Affects Your SEO (exploit)
- Maximize onsiteProminence through strategic internal linking: Pages linked from your homepage and top-level navigation receive higher prominence scores. Your most important content should be reachable within 1-2 clicks from the homepage. Restructure your navigation and add contextual internal links from high-prominence pages to push ranking weight to target pages.
- Prevent title rewrites by optimizing titleQualityScore: Google rewrites titles that score poorly on this signal. Keep titles under 60 characters, avoid keyword stuffing, and ensure the title accurately reflects page content. When Google rewrites your title, it often reduces CTR. Check Search Console for title discrepancies.
- Place target terms in headings to boost avgTermWeight: The DOJ confirmed that terms in titles receive the highest weight, followed by headings (H1-H6), then body text. Font size and bold formatting also factor in. Use your primary keyword in the H1, secondary keywords in H2s, and ensure heading hierarchy is semantic, not decorative.
- Optimize for passage-level indexing: Google indexes and ranks individual page sections independently. Structure content with clear H2/H3 sections that each answer a specific question. Each passage should be self-contained enough to serve as a featured snippet. This means a single well-structured page can rank for multiple queries.
- Score well on imageLayoutScore by integrating visuals with text: This signal evaluates image placement relative to text content. Place relevant images near the text they illustrate, use descriptive alt text, and maintain a good image-to-text ratio. Pages with no images score lower on content richness (numImages is tracked separately).
- Avoid tagPageScore penalties on taxonomy pages: Google specifically scores tag and category pages for quality. Auto-generated tag pages with thin content or duplicate listings hurt your site. Either make taxonomy pages genuinely useful with unique descriptions and curated content, or noindex low-value tag pages.

### onsiteProminence (Leak)
Tracks how prominently a page is linked from the homepage and site structure. Internal linking hierarchy directly affects ranking weight. Pages linked from the homepage or top-level navigation receive higher prominence scores than those buried deep in the site.
Formula: onsiteProminence(page) = link_depth(
homepage, page, nav_structure
)  // closer to homepage = higher score
if in_top_nav: boost prominence
// internal linking hierarchy = ranking weight
Patents: US8818982 (page segmentation)
Affects: Affected by: homepage link placement, internal link depth, navigation structure, breadcrumb hierarchy

### titleQualityScore (Leak)
Predicts title presentation quality for search results. Low scores trigger automatic title rewriting by Google. Evaluates title length (via titleSize), keyword stuffing, relevance to page content, and readability for SERP display.
Formula: titleQualityScore(page) = evaluate(
titleSize, keyword_density, content_match
)
if score < threshold: rewrite title_for_SERP
// Google rewrites low-quality titles automatically
Patents: US8818982 (page segmentation)
Affects: Affected by: title length, keyword density, content-title alignment, readability, SERP display fitness

### avgTermWeight (DOJ)
Term importance weighting within a document. Measures where terms appear (title, headings, body text) and their visual prominence (font size, bold, etc.). Terms in titles and headings receive higher weight than body text occurrences.
Formula: avgTermWeight(term, doc) = weighted_avg(
title_weight * freq_title,
heading_weight * freq_h1_h6,
body_weight * freq_body
)  // title > heading > body
// visual prominence (font size, bold) also factors
Patents: US8818982 (page segmentation), US8645362 (resource load times)
Affects: Affected by: term position (title > heading > body), font size, HTML tag hierarchy, term frequency

### imageLayoutScore (Leak)
Evaluates visual content placement and layout quality on the page. Assesses how images are positioned relative to text content and overall page structure. Exact scoring criteria not confirmed.
Formula: imageLayoutScore(page) = eval_layout(
img_positions, text_proximity, numImages
)  // image-text relationship quality
// combined with numImages for content richness
Patents: US8818982 (page segmentation)
Affects: Affected by: image placement, image-text relationship, layout structure, visual content quality

## Pipeline
- **Page Parse**: HTML segmentation via DomDistiller-style heuristics: link density, text density, semantic HTML tags, CSS class blacklists. Separates main content from nav, footer, sidebar, ads. See Content Extraction dive for the full algorithm.
- **Title Analysis**: titleQualityScore, titleSize, query-title match
- **Content Scoring**: avgTermWeight, pageClassification, passage extraction
- **Visual Layout**: imageLayoutScore, numImages, content richness
- **Site Context**: onsiteProminence, tagPageScore, internal hierarchy

## Timeline
- 2005: Page Segmentation - Google patents methods for identifying main content blocks vs boilerplate (headers, footers, navigation). VIPS algorithm (US7428700). Later formalized in DomDistiller (Chromium) and boilerplate detection patents (US8898296, US8041713).
- 2009: Featured Snippets Precursor - Patents on extracting and presenting direct answers from page content. Structured extraction from HTML.
- 2013: Title Quality Signals - Google begins rewriting search titles when original titles are low quality, too long, or keyword-stuffed.
- 2019: Passage Ranking - Google patents passage-level indexing and ranking. Individual sections of a page can rank independently.
- 2024: API Leak: 8 Structure Signals - onsiteProminence, titleQualityScore, snippetPrefixCharCount, imageLayoutScore, numImages, pageClassification confirmed.
- 2024: DOJ: Three Pillars - Body (document content) confirmed as first of Three Pillars. avgTermWeight measures term importance within document.

## Contradictions
- Public (Google Public): "We don't rewrite page titles. We use what webmasters provide."
  Internal (API Leak): titleQualityScore actively evaluates titles. Low-scoring titles are rewritten for search results. titleSize tracks length violations.
- Public (Google Public): "Internal linking structure doesn't significantly affect rankings."
  Internal (API Leak): onsiteProminence measures how prominently a page is linked from the homepage and site structure. Pages buried deep in site hierarchy receive less ranking weight.
- Public (Google Public): "Images are primarily for user experience, not a ranking factor."
  Internal (API Leak): imageLayoutScore and numImages are tracked as ranking signals. Visual content quality and placement directly affect content quality scoring.
