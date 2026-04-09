# WebMirror: Duplicate Detection and Canonicalization

## Key Insight
WebMirror operates at massive scale with a deceptively simple core mechanism: SimHash fingerprinting. Each document gets a 64-bit fingerprint based on 5-gram word sequences (shingling). Documents with fingerprints differing in 3 or fewer bits (k=3) are considered near-duplicates and grouped into clusters via duplicateClusterId. Then a ~40-signal ML model selects the canonical URL from each cluster. The localizedCluster signal extends this to multi-language pages, handling hreflang relationships. Non-canonical pages are either excluded from the index or marked as alternates. The DOJ confirmed shingleInfo in CompressedQualitySignals, meaning duplicate detection directly affects quality scoring.

### 10 Patents (patent)
- Patent US7711679: Phrase-based detection of duplicate documents (Anna Lynn Patterson) - Uses phrase analysis rather than just word-level comparison for more accurate duplicate detection
- Patent US6658423: Detecting duplicate and near-duplicate files (William Pugh, Monika Henzinger) - Core near-duplicate detection using fingerprinting
- Patent US6138113: Method for identifying near duplicate pages in a hyperlinked database (Jeffrey Dean, Monika Henzinger) - Foundation of SimHash-based deduplication
- Patent US7680773: Automatically managing duplicate documents when crawling dynamic documents (Anurag Acharya, Arvind Jain) - Handles URL parameters, session IDs, and dynamic content that create false duplicates
- Patent US7185088: Removing duplicate search engine results - Deduplication at the SERP level to prevent users seeing the same content multiple times
- 37 patents total spanning from 1996 (US5745900, database fingerprinting) to modern era. The volume reflects the scale challenge: billions of pages require efficient deduplication
- US7146416: Web site activity monitoring system with tracking by categories and terms; US7062482: Techniques for phonetic searching; US7366718: Detecting duplicate and near-duplicate files
- US8001462: Updating search engine document index based on calculated age of changed portions in a document

### API Leak: 3 WebMirror Signals (leak)
- WebMirror (WebMirror module): Core canonicalization and deduplication system. Tier 1 match, 100/100 corroboration, 17 matched patents
- duplicateClusterId (IndexingDocjoinerDataVersion): Assigns each page to a duplicate content cluster. Used for canonical URL selection. Tier 1, 100/100 corroboration, 37 matched patents
- localizedCluster (IndexingDupsLocalizedLocalizedCluster): Multi-language page relationship grouping for hreflang deduplication. Groups translated versions of the same content. Tier 2, 54/100 corroboration, 17 patents
- All three signals categorized under technical-crawl, confirming WebMirror operates in the crawl/index pipeline, not at ranking time

### DOJ Trial: Shingling Confirmation (doj)
- shingleInfo (CompressedQualitySignals): Duplicate content detection via overlapping word sequences (shingling). Confirmed as cross-reference evidence
- Module placement in CompressedQualitySignals means duplicate detection feeds directly into quality scoring, not just index management
- Near-duplicate pages don't just get deduplicated; they can affect the quality score of the surviving canonical page
- The DOJ categorizes shingleInfo under spam-content, confirming Google treats content duplication as a quality/spam signal

### How Duplicate Detection Affects SEO (exploit)
- Canonical selection uses ~40 signals: Google's ML model considers content quality, link signals, traffic, freshness, and domain authority when picking the canonical. The best version wins, not necessarily the original
- Near-duplicate threshold is k=3 bits: SimHash with 64-bit fingerprints and 3-bit tolerance catches paraphrased content, not just exact copies. Minor rewording won't help
- Shingling uses 5-grams: Overlapping sequences of 5 consecutive words create the fingerprint. This catches paragraph-level copying even with different surrounding text
- hreflang clusters matter: localizedCluster groups translated pages correctly, but misconfigured hreflang can cause Google to pick the wrong language version as canonical
- Duplicate pages waste crawl budget: Non-canonical pages in your duplicate cluster still get crawled but aren't indexed. URL parameters, session IDs, and faceted navigation create unnecessary duplicates
- Quality score impact: shingleInfo in CompressedQualitySignals means scraped/duplicated content can lower your site's quality score, not just lose the individual page
- Syndicated content risk: If you syndicate content to higher-authority sites, their version may become canonical. Use rel=canonical pointing to your original

### SimHash Fingerprinting (Patent + Leak)
Each document is converted to a 64-bit fingerprint using SimHash. The document is broken into overlapping 5-word sequences (shingles). Each shingle is hashed, and the hash values are combined into a single 64-bit signature. Two documents with signatures differing in 3 or fewer bits (Hamming distance <= 3) are considered near-duplicates.
Formula: fingerprint(doc) = SimHash(
  shingles(doc, n=5)
)  // 64-bit output

isNearDuplicate(a, b) =
  hamming(
    fingerprint(a), fingerprint(b)
  ) // k=3 bit tolerance
// Catches paraphrasing, not just exact copies
Patents: US6138113 (Dean, Henzinger), US6658423 (Pugh, Henzinger)
Affects: Affected by: word-level content changes, content length, language. Minor rewording within 3-bit tolerance still detected as duplicate

### Canonical Selection (~40 Signals) (Leak + Indexing Deep Dive)
When multiple pages are clustered as duplicates, a machine learning model evaluates approximately 40 signals to select the canonical URL. Signals include: content completeness, domain authority, PageRank, link signals, page speed, HTTPS, user-preferred URL, rel=canonical hints, sitemap declarations, redirect chains, and historical indexing data.
Formula: canonical(cluster) = ML_select(
  pages ∈ cluster,
  ~40 signals
)
// Signals: content quality, authority, links
// PageRank, HTTPS, rel=canonical hints
// User preferences, sitemap, redirects
// Non-canonical pages excluded from index
Patents: US7711679, US7185088
Affects: Affected by: domain authority, content completeness, HTTPS status, rel=canonical hints, sitemap signals, redirect configuration

### Localized Cluster (hreflang) (Leak)
The localizedCluster signal groups translated versions of the same content into language-specific clusters. This prevents a French translation from competing with the English original for the same canonical slot. Each language version can be canonical within its own localized cluster, allowing proper international indexing.
Formula: localizedCluster(page) =
  group(
    hreflang_annotations,
    language_detection,
    content_similarity
  )
// Groups: en, es, fr, de versions
// Each language has its own canonical
// Misconfigured hreflang → wrong canonical
Affects: Affected by: hreflang annotation accuracy, content translation quality, language detection, URL structure

## Pipeline
- **Crawl & Content Extraction**: Trawler crawls pages. Raw HTML extracted and rendered (if JS-dependent). Text content passed to WebMirror.
- **Shingling (5-gram)**: Document broken into overlapping 5-word sequences. Each shingle hashed individually.
- **SimHash Fingerprinting**: Shingle hashes combined into 64-bit SimHash fingerprint. Compact representation of document content.
- **Near-Duplicate Clustering**: Pages with fingerprints differing by <= 3 bits grouped into clusters. duplicateClusterId assigned.
- **Localized Clustering**: Multi-language versions grouped via hreflang and content similarity. localizedCluster separates language variants.
- **Canonical Selection (ML)**: ~40-signal ML model selects canonical URL per cluster. Non-canonical pages excluded from index or marked as alternates.
- **Quality Score Impact**: shingleInfo feeds into CompressedQualitySignals. Scraped/duplicate content affects site-wide quality scoring.

## Timeline
- 1996: Database Fingerprinting - US5745900 (Michael Burrows): Method for indexing duplicate database records using full-record fingerprints. AltaVista era.
- 1998: SimHash Near-Duplicate Detection - US6138113 (Jeffrey Dean, Monika Henzinger): Method for identifying near-duplicate pages using fingerprinting. Foundation of WebMirror.
- 2000: Query-Specific Deduplication - US6615209 (Benedict Gomes, Ben Smith): Detecting duplicates per query, not globally. Different queries may surface different canonicals.
- 2004: Phrase-Based Detection - US7711679 (Anna Lynn Patterson): Phrase-level duplicate detection, more accurate than word-level. Catches paraphrased content.
- 2005: Dynamic Document Dedup - US7680773 (Anurag Acharya, Arvind Jain): Handling URL parameters, session IDs, and dynamic content that create false duplicates.
- 2024: Three Signals Revealed - API leak exposes WebMirror, duplicateClusterId (37 patents), and localizedCluster. SimHash with 5-gram shingling and k=3 threshold confirmed.
- 2024: shingleInfo in Quality - DOJ confirms shingleInfo in CompressedQualitySignals. Duplicate detection directly affects quality scoring, not just index management.
