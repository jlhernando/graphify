# IndyRank: Link Independence Scoring

## Key Insight
The 2024 API leak revealed four IndyRank variants operating at different granularities. The core IndyRank signal (CompositeDoc, Tier 1) measures per-document link independence with 65/100 corroboration across 5 patents. ScaledIndyRank (QualityNsrNsrData) operates at the site level through NSR, meaning your domain's overall link independence profile affects every page. Two experimental variants, ScaledExptIndyRank2 and ScaledExptIndyRank3 (both PerDocData), suggest Google is actively refining the algorithm. IndyRank is related to PageRank, Q*, SpamBrain, NSR, and Anchors, making it a cross-cutting quality signal that touches multiple ranking pillars.

### Patent Evidence (patent)
- Patent US7024451: Maintaining consistent independent server-side state - Foundation for computing independence metrics across distributed link graph
- Patent US7958128: Link independence measurement - Scoring how independently different linking domains discovered a target page
- 5 patents total confirmed via API leak cross-reference. IndyRank operates alongside PageRank_NS (seed distance) as complementary link quality metrics
- The independence model shares mathematical foundations with TrustRank (US7603350): both evaluate link graph topology rather than raw volume

### API Leak: Four IndyRank Variants (leak)
- IndyRank (CompositeDoc): Core per-document link independence score. Tier 1 trust, 65/100 corroboration
- ScaledIndyRank (QualityNsrNsrData): Site-level independence via NSR. Domain-wide backlink independence affects all pages
- ScaledExptIndyRank2 (PerDocData): Experimental variant 2 - per-document with alternate scaling. 16/100 corroboration (in testing)
- ScaledExptIndyRank3 (PerDocData): Experimental variant 3 - per-document with alternate scaling. 16/100 corroboration (in testing)
- All four variants confirmed as real signals. The 'Scaled' prefix suggests normalization across different site sizes
- IndyRank is categorized under link-authority, confirming it directly affects authority scoring

### Related DOJ Evidence (doj)
- No direct DOJ testimony names 'IndyRank' explicitly, but the concept of link independence is supported by multiple trial themes
- Pandu Nayak testified that Google uses sophisticated link quality evaluation beyond simple counting
- HJ Kim confirmed that link signals are one of three pillars (Anchors in T* = Anchors + Body + Clicks)
- DOJ evidence on SpamBrain shows link manipulation detection, which IndyRank directly supports by identifying non-independent link patterns

### How Link Independence Affects Rankings (exploit)
- Diversity beats volume: 50 independent editorial links from unrelated sites > 500 links from a coordinated network
- Industry cross-pollination: Links from sites in different verticals signal genuine value, not reciprocal arrangements
- Domain-level impact via NSR: ScaledIndyRank in QualityNsrNsrData means your site's overall link independence affects every page's quality score
- PBNs are detectable: Private blog networks create non-independent link patterns that IndyRank is designed to identify
- Natural editorial links win: Content that earns links from journalists, researchers, and bloggers who discovered it independently scores highest
- Link building strategy: Focus on creating genuinely useful content that different communities link to for different reasons
- Experimental variants suggest evolution: ScaledExptIndyRank2/3 indicate Google is actively refining independence measurement

### IndyRank Independence Score (Leak)
IndyRank evaluates the independence of a page's backlink profile. Rather than counting links, it measures whether linking domains discovered the target independently or through shared pathways (e.g., the same link list, the same PBN, the same guest post exchange). High independence = high IndyRank.
Formula: IndyRank(doc) = independence(
  linking_domains(doc),
  graph_connectivity(linking_domains)
)
// High: diverse, unrelated linking domains
// Low: clustered, interconnected link sources
// Feeds into Q* and SpamBrain scoring
Patents: US7024451, US7958128
Affects: Affected by: diversity of linking domains, cross-industry link sources, absence of reciprocal patterns, editorial nature of links

### ScaledIndyRank (Site-Level) (Leak)
ScaledIndyRank normalizes the independence score at the site level via NSR (Normalized Site Rank). This means a domain with mostly independent, editorial backlinks will have all its pages benefit from a higher baseline quality score. Conversely, a domain with coordinated or purchased link patterns will see a site-wide quality penalty.
Formula: ScaledIndyRank(site) =
  normalize(
    IndyRank(aggregate_links(site)),
    site_size
  )
// Normalized for site size (small sites vs large)
// Feeds into NSR quality evaluation
// Affects Q* site-wide quality score
Affects: Affected by: domain-wide link profile diversity, site size normalization, NSR quality pipeline

## Pipeline
- **Link Graph Construction**: All inbound links to a page/domain collected from the crawl graph. Each linking domain identified as a potential independent source.
- **Independence Analysis**: Graph connectivity between linking domains evaluated. Domains that link to each other or share common link patterns flagged as potentially non-independent.
- **Per-Document IndyRank**: Raw IndyRank score computed for each document (CompositeDoc). Measures what fraction of linking domains appear truly independent.
- **Site-Level Aggregation**: ScaledIndyRank aggregates per-document scores at the site level via NSR. Normalized for site size to compare small and large sites fairly.
- **Quality Integration**: IndyRank feeds into Q* quality scoring and SpamBrain spam detection. Low IndyRank triggers further link spam investigation.
- **Experimental Refinement**: ScaledExptIndyRank2/3 variants test alternative scaling and independence algorithms. Active R&D on link independence measurement.

## Timeline
- 2004: Early Independence Research - US7024451 filed: foundations for computing independence metrics across distributed systems.
- 2008: Link Independence Patent - US7958128: formal method for measuring how independently different domains discovered a target page.
- 2012: Penguin Context - Google Penguin update targeted unnatural link patterns. IndyRank-style independence scoring likely informed the algorithm.
- 2024: Four Variants Revealed - API leak exposes IndyRank, ScaledIndyRank, ScaledExptIndyRank2, ScaledExptIndyRank3. Site-level variant in NSR confirmed.
