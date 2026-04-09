# TeraGoogle: The Long-Term Web Archive

## Key Insight
TeraGoogle achieves 100% corroboration (Tier 1) across three independent sources. The API leak places it in the TeraGoogle module as 'long-term memory storage for secondary indexing.' The DOJ classifies it under Infrastructure/Storage as 'long-term document storage for maintaining the full web archive.' Patent US7346839 (by Anurag Acharya, Matt Cutts, Jeffrey Dean, Paul Haahr, and others) describes using historical data for document scoring, which relies on TeraGoogle's archive. The key implication: Google's ranking system has access to your site's entire history, including past content, historical link profiles, domain ownership changes, and quality trajectory over time.

### Patent Evidence (20 Patents) (patent)
- Patent US7346839: Information retrieval based on historical data (Acharya, Cutts, Dean, Haahr, Henzinger, Hoelzle, Lawrence, Pfleger, Sercinoglu, Tong) - 56 claims. System scores documents based on history: content changes, link acquisition rate, domain registration, anchor text evolution. Requires TeraGoogle's archive.
- Patent US6285999: Method for node ranking in a linked database - PageRank foundation. Historical link data from TeraGoogle enables tracking link graph evolution.
- Patent US7716225: Reasonable Surfer - Link valuation model that benefits from historical click pattern data stored in TeraGoogle.
- Patent US7058628: Systems and methods for providing search results - Historical document data used for result quality assessment.
- 20 patents total, many by Google's founding engineers (Dean, Henzinger, Haahr). These patents consistently reference historical document data that requires persistent storage at web scale.

### API Leak: TeraGoogle Signal (leak)
- TeraGoogle (TeraGoogle module): Long-term memory storage mechanism for secondary indexing. Tier 2 match, 100/100 corroboration, 20 matched patents.
- Categorized under technical-crawl, placing it in the infrastructure layer alongside Trawler and SegIndexer.
- The 'secondary indexing' description confirms TeraGoogle as the backup/archive layer behind SegIndexer's serving index.
- 20 patent matches indicate TeraGoogle is deeply integrated into Google's document processing and historical analysis systems.

### DOJ Trial: Infrastructure Confirmation (doj)
- TeraGoogle (Storage module, Infrastructure system): Confirmed as 'long-term document storage system for maintaining the full web archive.' Cross-reference evidence.
- Placed under Infrastructure system in DOJ documentation, alongside Trawler (crawling), SegIndexer (serving index), and other core systems.
- The 'full web archive' description confirms TeraGoogle stores everything Google has crawled, not just currently-indexed documents.
- Historical document access enables Google to evaluate: domain age, content evolution, link acquisition patterns, penalty history, and ownership changes.

### How Historical Data Affects Rankings (exploit)
- Google remembers deleted content: Pages you've removed, thin content you've replaced, and spam you've cleaned up are still in TeraGoogle. Historical quality trajectory matters.
- Link acquisition rate is tracked: Patent US7346839 explicitly scores based on link velocity. Sudden spikes in backlinks are detectable against historical patterns. Natural growth rewarded.
- Domain history persists: Buying an expired domain doesn't erase its history. Previous penalties, spam associations, and quality scores from the old owner are accessible via TeraGoogle.
- Content freshness signals need history: The Freshness system compares current content against historical versions stored in TeraGoogle to measure meaningful updates vs. cosmetic changes.
- Anchor text evolution tracked: Changes in your backlink anchor text profile over time are visible. Sudden pivot to exact-match anchors is detectable against your historical profile.
- Quality trajectory matters: A site consistently improving content quality over years builds a positive historical signal. A site that degraded from high-quality to thin content has a negative trajectory.
- Penalty recovery takes time: Even after fixing issues, the historical record of problems persists. Recovery requires sustained quality improvement to override historical negative signals.
- 400B+ documents archived: TeraGoogle stores the complete archive at petabyte scale. Nothing crawled is ever truly forgotten.

### Historical Data Scoring (US7346839) (Patent)
Documents are scored based on multiple types of historical data stored in TeraGoogle. The patent identifies several historical factors: when the document was first indexed, how its content has changed over time, how its link profile has evolved, domain registration and renewal patterns, and anchor text changes. The system generates a score that adjusts the document's ranking based on these temporal patterns.
Formula: HistoryScore(doc) = f(
  first_indexed_date,
  content_change_rate,
  link_acquisition_velocity,
  domain_registration_history,
  anchor_text_evolution
)
// Steady growth > sudden spikes
// Consistent quality > volatile quality
// Historical data from TeraGoogle archive
Patent: US7346839 (56 claims, 10 inventors including Cutts, Dean, Haahr)
Affects: Affected by: domain age, content update patterns, link velocity, registration stability, anchor text consistency

### Tiered Storage Architecture (Leak + DOJ + Infrastructure Deep Dive)
Google uses a tiered storage architecture. SegIndexer maintains the actively-served index in three tiers: Base (high-quality), Zeppelins (mid-tier), and Rafts (low-priority). TeraGoogle sits behind all tiers as the persistent archive. Documents can move between tiers based on quality and freshness, but TeraGoogle always retains the historical record.
Formula: Storage = {
  SegIndexer: {
    Base:      // High-quality, fast serving
    Zeppelins: // Mid-tier, slower access
    Rafts:     // Low-priority, supplemental
  },
  TeraGoogle: // Complete archive, petabytes
                // Historical data for all docs
                // Never deleted, always accessible
}
Affects: Affected by: document quality tier, freshness, crawl frequency. TeraGoogle retains documents even when they drop from serving index.

## Pipeline
- **Trawler Crawl**: Trawler fetches pages from the web. Raw content passed to processing pipeline.
- **Processing & Rendering**: Content processed, JavaScript rendered (HtmlrenderWebkit), metadata extracted, quality signals computed.
- **WebMirror Deduplication**: Duplicates detected via SimHash. Canonical URLs selected. Non-canonical pages marked.
- **TeraGoogle Archive**: ALL processed documents stored in TeraGoogle persistent archive. Historical versions maintained. Nothing is deleted.
- **SegIndexer Tiering**: Qualifying documents promoted to serving index tiers (Base/Zeppelins/Rafts). Quality and freshness determine tier placement.
- **Historical Analysis**: Ranking systems query TeraGoogle for historical data: content changes, link evolution, domain history. Feeds US7346839 scoring.

## Timeline
- 2003: Historical Data Patent Filed - US7346839 filed by Acharya, Cutts, Dean, Haahr et al. 56 claims describing historical document scoring. Requires persistent archive (TeraGoogle).
- 2006: Historical Data Patent Granted - US7346839 granted. Google can now officially score documents based on content change patterns, link velocity, and domain history.
- ~2010: Petabyte-Scale Storage - TeraGoogle scales to petabytes to accommodate the growing web. 400B+ documents archived for historical analysis.
- 2024: API Leak Confirmation - TeraGoogle confirmed as 'long-term memory storage mechanism for secondary indexing.' 100/100 corroboration, 20 matched patents.
- 2024: DOJ Infrastructure Evidence - DOJ documents confirm TeraGoogle under Infrastructure/Storage as 'long-term document storage system for maintaining the full web archive.'
