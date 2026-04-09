# SiteDiversity: SERP Diversity Mechanism

## Key Insight
SiteDiversity operates as a PredocTwiddler, processing results in the early post-ranking stage where fast heuristics handle deduplication and diversity across hundreds of URLs. The typical limit is 2-3 results per domain per SERP page, though this varies by query type: navigational queries allow more results from the target domain, while informational queries enforce stricter diversity. SiteDiversity also interacts with subdomain handling: Google sometimes treats subdomains as separate sites (e.g., blog.example.com vs shop.example.com) and sometimes as the same site, depending on whether the subdomain has independent content and link profiles. The system was partially relaxed for site: queries and navigational searches, where users explicitly want results from a specific domain.

### 5 Patents (patent)
- Document ranking via word relationships: builds global and local term relationship graphs to enforce topical diversity in search results (US8321409)
- Link-based document clustering: groups hyperlinked documents into topic clusters for diverse result presentation (US7213198)
- Category diversity enforcement - Patent US10289648 (Arora, Yang, Yang, filed 2011): Prevents POI category over-representation by applying rank-based scaling factors. Top-ranked entities in under-represented categories promoted (e.g., museums scaled to 130%), lower-ranked entities in over-represented categories demoted. Categories with >2 candidates trigger scaling. Re-ranks by combined scaled scores across all categories
- The Twiddler framework patents describe pluggable re-ranking functions, which is the architecture SiteDiversity uses.
- Query-dependent diversity patents describe varying diversity thresholds based on query intent: stricter for informational, relaxed for navigational.
- US8620951: Search query results based upon topic; US11314822: Interface for a universal search

### Related Leak Evidence (leak)
- No direct SiteDiversity signals in the API leak, but the Twiddler framework it operates within is documented:
- YouTubeDensityTwiddler (DOJ): A specific density limiter for YouTube results, confirming the pattern of per-source result caps in the Twiddler framework.
- BlogCategorizer (DOJ): Content type classification that feeds into diversity decisions. Blog content may be subject to different diversity rules than news or product pages.
- PredocTwiddlers (DOJ): The early-stage Twiddler category where SiteDiversity likely operates, processing hundreds of URLs with fast heuristics for dedup and diversity.
- BadURLsCategorizer: Problematic URL identification. Bad URLs from a domain may reduce that domain's diversity allocation.

### Related DOJ Context (doj)
- No direct SiteDiversity signals in DOJ trial evidence, but the diversity enforcement system was confirmed through related mechanisms:
- PredocTwiddlers: Confirmed as fast heuristic processors handling deduplication and diversity on hundreds of URLs. SiteDiversity operates at this stage.
- YouTubeDensityTwiddler: Explicitly prevents YouTube from dominating SERPs, confirming Google has per-source density caps. SiteDiversity applies the same pattern to all domains.
- DOJ testimony on Twiddlers confirmed they handle 'quality boosts, diversity enforcement, content filtering, and specialized result treatment,' directly referencing diversity as a core Twiddler function.

### How SiteDiversity Affects Your SEO (exploit)
- Avoid cannibalizing your own rankings: SiteDiversity limits you to typically 2-3 results per SERP. If you have 5 pages targeting the same keyword, only 2-3 will appear, and the others are wasted. Consolidate overlapping content into fewer, stronger pages rather than spreading thin across many.
- Use subdomains strategically for diversity: Google sometimes treats subdomains as separate sites. If you have distinct content verticals (blog, docs, shop), hosting them on subdomains may allow more SERP slots. But this only works if the subdomain has genuinely independent content and its own link profile.
- Target multiple query intents per topic: Instead of creating 5 informational pages on the same topic, create pages that target different intents (informational, transactional, comparison). Different intents may land you in different SERP features, effectively bypassing the diversity limit.
- Navigational queries are your exception: SiteDiversity is relaxed for navigational/brand queries. If users search for your brand name, you can dominate the SERP with homepage, product pages, support pages, and social profiles. Build strong brand recognition to trigger navigational classification.
- Monitor for cannibalization signals: If your pages are alternating in rankings (page A ranks one day, page B the next, never both), SiteDiversity is likely limiting you. This is a signal to consolidate those pages or differentiate their targeting.
- Leverage SERP features for extra slots: SiteDiversity applies to organic blue links, but featured snippets, video carousels, image packs, and PAA are separate feature types in Tangram. A page in the featured snippet plus a page in organic results effectively gives you two slots despite diversity limits.

### SiteDiversity Filtering (Observable + Twiddler Framework)
After initial ranking by Mustang/Ascorer, the SiteDiversity Twiddler scans the result set and enforces per-domain limits. Results beyond the limit are pushed to later pages or suppressed. The limit varies by query classification: navigational queries allow more same-domain results, informational queries enforce stricter limits (typically 2-3 per domain).
Formula: SiteDiversity(ranked_results) =
for each domain in results:
  if count(domain) > max_per_domain:
    demote(excess results)
// max_per_domain ~2-3 for informational
// relaxed for navigational/brand queries
// operates as PredocTwiddler (fast, early stage)
Affects: Affected by: query intent classification (navigational vs informational), number of pages from your domain in candidate results, subdomain treatment, content differentiation between your pages.

### Subdomain Independence Check (Observable)
Google determines whether a subdomain should be treated as part of the parent domain or as an independent site for diversity purposes. Factors include whether the subdomain has its own distinct content, independent link profile, separate site in Search Console, and different topical focus. Truly independent subdomains may receive their own diversity allocation.
Formula: SubdomainCheck(subdomain) =
  isIndependent(
    content_overlap,
    link_profile,
    topical_focus
  )
if independent: separate diversity slot
else: counts toward parent domain limit
Affects: Affected by: subdomain content independence, separate link profiles, distinct topical focus, Search Console configuration.

### Category Diversity Scaling (Patent US10289648 (Arora, Yang, Yang, 2011))
For POI searches, prevents category over-representation by applying rank-based scaling factors to relevance scores. Categories with more than a threshold number of candidates (e.g., >2) trigger scaling. Top-ranked entities in under-represented categories receive promotion factors (>1.0), lower-ranked entities in over-represented categories receive demotion factors (<1.0). Results re-ranked by scaled scores across all categories.
Formula: ScaledScore(poi) =
  relevance(poi) ×
  scalingFactor(category, rank)
// Example scaling factors:
// Top restaurant: 120%, 2nd restaurant: 80%
// Top museum: 130% (boost under-represented)
if count(category) > threshold:
  apply scaling table
// re-rank by combined scaled scores
Patent: US10289648 (Enforcing category diversity, 2011)
Affects: Affected by: category distribution in result set, rank position within category, number of candidates per category, scaling factor configuration.

### Term Relationship Diversity (Patent)
Builds a global term relationships graph capturing how terms co-occur and relate across the web corpus. For a given query, constructs local term relationships from the candidate result set. Documents are then scored not just on relevance but on how much they contribute to topical diversity relative to other results already selected. This prevents results that cover the same subtopic from monopolizing the SERP.
Formula: diversityScore(doc, selected) = relevance(doc) ×
novelty(
localTerms(doc) - coveredTerms(selected),
globalTermGraph
)  // rewards documents covering new subtopics
// penalizes redundancy with already-selected results
Patents: US8321409 (document ranking using word relationships)
Affects: Affected by: topical breadth of content, subtopic coverage relative to competing results, term relationship graph density for the query topic.

### Link-Based Topic Clustering (Patent)
Groups hyperlinked documents into topic clusters based on their link structure. Pages that link to each other heavily are assumed to cover related topics. The clustering enables diversity enforcement at the topic level: the system selects results from different clusters rather than returning multiple pages from the same tightly-linked cluster.
Formula: cluster(docs) = linkPartition(
linkGraph(docs)
) → [cluster_1, ..., cluster_K]
diverseResults = selectTopFrom(
each cluster
)  // one top result per topic cluster
Patents: US7213198 (link-based clustering of hyperlinked documents)
Affects: Affected by: link graph topology, inter-site linking patterns, topical cluster membership. Pages in unique link clusters get separate diversity slots.

## Pipeline
- **Mustang Retrieval**: Initial document retrieval produces thousands of candidates. Multiple pages from the same domain may score highly.
- **Ascorer Ranking**: Full ranking with ~1,700 signals. Domain-dominant topics may have 5-10 results from a single site in top positions.
- **PredocTwiddler Stage**: SiteDiversity operates here. Per-domain limits enforced. Excess same-domain results demoted or pushed to later pages.
- **Content Type Diversity**: YouTubeDensityTwiddler and BlogCategorizer apply content-type-specific diversity limits alongside domain diversity.
- **Tangram Assembly**: SERP features (snippets, images, videos) assembled separately from blue links. A domain may appear in both organic and features.

## Timeline
- 2003: Host Crowding Prevention - Early Google algorithms limit same-host results in SERPs. Basic deduplication and diversity enforcement introduced.
- 2004: Link-Based Document Clustering (US7213198) - Georges Harik (Google) patents link-based clustering of hyperlinked documents. Groups pages into topic clusters by link structure, enabling diversity enforcement at the topic level rather than just the domain level.
- 2008: Term Relationship Diversity (US8321409) - Sharad Jain (Google) patents document ranking using word relationships. Builds global and local term relationship graphs to score documents by topical novelty relative to already-selected results.
- 2011: Category Diversity Patent (US10289648) - Google patents POI category diversity enforcement. Rank-based scaling factors prevent category over-representation: under-represented categories boosted (130%), over-represented categories demoted. Re-ranks by scaled scores across all categories.
- 2012: Site Diversity Update - Google updates diversity algorithm. Sites limited to 2 results per SERP page for most queries. Navigational queries exempted.
- 2019: Site Diversity Launch (June) - Google announces formal 'site diversity' change limiting domains to 2 organic listings per page. Subdomains treated as same site in most cases.
- 2024: Twiddler Framework Confirmed - DOJ trial confirms PredocTwiddlers handle diversity enforcement. YouTubeDensityTwiddler confirms per-source density caps in the Twiddler architecture.
