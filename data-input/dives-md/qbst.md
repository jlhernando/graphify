# QBST: Query Based Salient Terms

## Key Insight
QBST is a memorization system, not a machine learning model. It has "memorized" which words appear prominently on pages that users click for specific queries. This makes it extremely fast for initial retrieval. The system generates the first candidate set of potentially hundreds of thousands of documents, which Mustang then scores and reduces. Combined with salientTermSet and salientTermReferenceScore from the API leak, QBST represents Google's term-relevance foundation beneath all neural ranking.

### Signal (Sworn Testimony) (doj)
- QBST - Query Based Salient Terms, testified by Pandu Nayak
- Memorization system, not ML (explicitly stated)
- Trained on query and click data correlations
- Used in initial retrieval phase for candidate generation
- Identifies words appearing prominently on relevant pages
- Part of the pre-Mustang retrieval pipeline

### Salient Term Signals (leak)
- salientTermSet - Extracted salient terms with weights for topical relevance
- salientTermReferenceScore - Term weights compared against reference corpus
- titlematchScore - Query-to-title term matching across site
- avgTermWeight - Average weighted font size of terms in body
- termWeight - Individual word size/bolding impact on scoring
- All in Mustang module, implementing QBST-style term relevance

### 14 Patents (patent)
- Phrase-based indexing for multi-word concept matching
- TF-IDF and BM25 foundations for term frequency scoring
- Term proximity signals for phrase-level relevance
- Query expansion via related salient terms
- Inverted index lookup at massive scale for initial retrieval
- Click-through data for salient term identification and weighting
- US7617205: Estimating confidence for query revision models; US7933890: Propagating useful information among related web pages, such as web pages of a website; US6947930: Systems and methods for interactive search query refinement
- US7319994: Document compression scheme that supports searching and partial decompression; US7558787: Automatic relevance and variety checking for web and vertical search engines; US5826261: System and method for querying multiple, distributed databases by selective sharing of local rela...
- US6704727: Method and system for generating a set of search terms; US6014665: Method for organizing information; US20100169327: Tracking significant topics of discourse in forums
- US9830379: Name disambiguation using context terms; US8600973: Removing substitution rules; US8504562: Evaluation of substitute terms
- US7676460: Techniques for providing suggestions for creating a search query; US20050216478: Techniques for web site integration

### How QBST Affects Your SEO (exploit)
- Mine salient terms from top-ranking pages: QBST memorizes which words appear prominently on pages that users click. Extract common terminology from the top 10 results for your target query and ensure your content includes those terms naturally. Missing expected vocabulary means QBST may exclude you from the candidate set entirely.
- Use query-specific vocabulary, not just synonyms: Because QBST is a memorization system (not ML, per Nayak's sworn testimony), it relies on exact term correlations from click data. If top pages for 'best running shoes' consistently use 'cushioning,' 'pronation,' and 'drop,' your page needs those same terms to survive initial retrieval.
- Optimize titles site-wide, not just per page: The leak's titlematchScore is a site-level aggregate in QualityNsrNsrData. Poor titles on any page drag down the entire domain's score. Audit all page titles for descriptive, query-relevant language, especially thin or auto-generated pages.
- Emphasize terms visually with headings and bold text: The leak confirmed avgTermWeight and termWeight measure font size and bolding impact on scoring. Place your most important terms in H2/H3 headings and use bold/strong tags for key phrases. This directly feeds QBST-style term weighting in Mustang.
- Build all three T* pillars simultaneously: DOJ testimony confirmed T* (Topicality) combines Anchors (what the web says), Body (what you say), and Clicks (what users say). Earning descriptive anchor text from external links, writing comprehensive on-page content, and generating engagement signals all feed the same foundational retrieval score.
- Target featured snippets with structured answer formats: QBST's salientTermReferenceScore compares your term usage against a reference corpus. Pages that mirror the exact phrasing patterns of existing snippet content, using definition-style sentences, numbered lists, and direct question-answer structures, are more likely to match the memorized term patterns for snippet queries.

### QBST (DOJ)
Memorization system identifying words appearing prominently on relevant pages. Trained on query and click data. Used in initial retrieval phase to identify large candidate document set.
Formula: QBST(query) = lookup(
  termPageCorrelations[query.terms]
)  // memorization, NOT ML (Nayak sworn testimony)
// trained on query-click data: which terms appear on clicked pages
Patents: US7580929 (information retrieval), US8180754 (document scoring)
Affects: Affected by: use terminology that appears on high-ranking pages for your topics. Natural use of topic-relevant vocabulary.

### titlematchScore (Leak)
Measures how well page titles match user queries across the site. Part of QualityNsrNsrData in Mustang. Site-wide metric, not just per-page.
Formula: titlematchScore(site) = avg(
  titleRelevance(page, queries) for page in site
)  // QualityNsrNsrData, site-wide aggregate
// poor titles on any page drag down site-level score
Affects: Affected by: write descriptive, query-relevant titles. Consistent title quality across the site matters.

### T* (Topicality) (DOJ)
Query-dependent topicality computed through three sub-signals: Anchors (A) = what the web says about you, Body (B) = what you say about yourself, Clicks (C) = what users say about you. Foundation score in initial retrieval.
Formula: T*(doc, q) = combine(
  A(anchors), B(body), C(clicks)
)  // A=web says, B=you say, C=users say
// foundation retrieval score, all three pillars required
Affects: Affected by: all three pillars: anchor text from links, on-page content, and user engagement signals.

## Pipeline
- **Query Terms**: User query parsed into terms and salient phrases
- **QBST Lookup**: Memorized term-page associations from click data
- **Candidate Set**: Hundreds of thousands of potentially relevant documents
- **Mustang Scoring**: termWeight, salientTermSet, titlematchScore applied
- **RankEmbed**: Neural retrieval adds semantically similar documents

## Timeline
- 2003: Early Term Matching - Google uses basic TF-IDF and BM25 for document retrieval. Term frequency dominates relevance scoring.
- 2010: Phrase-Based Indexing - Bill Slawski documents phrase-based indexing patents. Google moves beyond single terms to multi-word concepts.
- 2013: Hummingbird - Semantic search shift. Query meaning matters more than exact term matching. QBST adapts to semantic retrieval.
- 2017: QBST Formalized - Query Based Salient Terms system codified as a memorization-based retrieval layer. Trained on query-click data.
- 2024: DOJ Trial: QBST Testified - Pandu Nayak explains QBST under oath as memorization system identifying prominent words on relevant pages.
- 2024: API Leak: Salient Terms - salientTermSet and salientTermReferenceScore confirmed in Mustang, implementing QBST-style term retrieval.

## Contradictions
- Public (Google Public): "Google Search is powered by advanced AI and machine learning."
  Internal (DOJ Trial): QBST is explicitly a memorization system, not ML. It memorizes term-page correlations from click data. The initial retrieval phase that generates candidate documents is fundamentally a lookup table, not AI.
- Public (Google Public): "Keyword matching is old technology. We understand meaning now."
  Internal (API Leak): salientTermSet, termWeight, titlematchScore, avgTermWeight are all active term-matching signals. Word-level matching with font size weighting remains foundational to Mustang scoring.
- Public (Google Public): "We don't use click data for ranking."
  Internal (DOJ Trial): QBST is literally "trained on query and click data." The system memorizes which terms appear on pages that users click. Click data is the foundation of the entire initial retrieval system.
