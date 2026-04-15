# Training Data Influence: What Gets Into LLMs and Why It Matters

## Key Insight
Training data creates permanent associations in the model's weights. Unlike RAG (real-time retrieval), training data influence is baked in and cannot be changed until the next model version. The hierarchy of influence: Wikipedia (most-cited source by ChatGPT, credibility tiebreaker when sources conflict), Reddit (40% of all citations, Q&A threads account for 50%+), news outlets (3x more correlated with AI visibility than brand content), academic papers (implicit authority weight), Stack Overflow/GitHub (tech entity associations). Common Crawl filtering uses Wikipedia as its quality proxy, meaning content that resembles Wikipedia's style is more likely to survive into training data.

### Training Data Composition (patent)
- Common Crawl: ~250B pages, 1-5% survives filtering. Harmonic Centrality prioritizes domains that reach many others through fewer link hops. Quality filtering uses perplexity scoring against Wikipedia as proxy. Key derivatives: C4 (T5), RefinedWeb (Falcon), FineWeb (15T tokens, HuggingFace). 67% of LLaMA training data
- Wikipedia: Most-cited source by ChatGPT. Second most-cited across all LLMs. Upsampled 5x+ vs natural proportion. Credibility tiebreaker when sources conflict. Having a Wikipedia page = existing as a formally recognized entity. 50% of top-cited agencies in AI marketing answers have Wikipedia pages. Wikidata/DBpedia provide machine-readable KG that LLMs query
- Reddit: 40% of all LLM citations (#1 cited domain). $60M/year deal with Google (Feb 2024). OpenAI also licensed. Tier 2 training data (3+ upvotes). Semrush study of 248K posts: 80% of cited posts have <20 upvotes, 70% have <20 comments. Q&A threads = 50%+ of citations. Topical alignment and clarity matter more than virality
- News outlets: Third-party mentions 3x more correlated with AI visibility than brand-owned content. Press releases less effective than earned media. OpenAI-Factiva partnership provides decades of Dow Jones archive. Feature stories > press releases for training data influence
- Code (GitHub, Stack Overflow): The Stack (3 TB, 30+ languages). Stack Exchange in The Pile. Tech brands: documented repos, answered questions with specific tool mentions create entity associations. README files, documentation crawled and indexed
- Academic papers: ArXiv, PubMed, S2ORC in training sets. Papers that coin terminology get embedded as reference knowledge. 2-5% of training mix but carry implicit authority weight

### Reddit Citation Mechanics (Semrush 248K Study) (leak)
- 80% of cited posts have <20 upvotes. Viral status not required. Median: 5-8 upvotes, 11-19 comments
- Q&A threads = 50%+ of all citations. Comparison and discussion posts together ~75%
- Average cited post is ~900 days old. Median length ~80 words. Brevity and clarity win
- Subreddit size doesn't correlate with citation probability. Small, focused subs work as well as large ones
- Entity-rich content (specific tools, versions, metrics) is supported as citation factor
- Funnel stage: Reddit appears in 36% mid-funnel, 27% bottom-funnel, 0% top-funnel queries
- Topical alignment is the #1 factor. Exact match to query topic matters most, not engagement signals

### How to Get Into Training Data (exploit)
- Wikipedia presence: Establish notability through independent reliable sources. Don't write your own article. Ensure sufficient third-party coverage exists for editors to cite. Maintain accuracy; promotional content rejected. Wikidata entries improve entity recognition
- Reddit strategy: Answer questions authentically in relevant subreddits. Provide specific tool/version mentions with context. Build karma organically. Focus on Q&A threads (50%+ of citations). Even 5-8 upvotes is sufficient. Write clearly in ~80 words
- Earn media coverage: Feature stories in trade publications > press releases. Third-party mentions 3x more effective than owned content. Target publications that are in Common Crawl and news training sets
- Publish original research: Academic papers, benchmark studies, industry surveys. Papers that coin frameworks become reference knowledge. ArXiv, PubMed in training sets
- GitHub/Stack Overflow for tech brands: Well-documented repos, answered questions with specific tool mentions. README files are crawled. Issue discussions create entity associations
- Content style: Write like Wikipedia (neutral, factual, well-structured). Common Crawl filtering uses Wikipedia as quality proxy. Content resembling Wikipedia style survives filtering at higher rates
- Timing matters: Content must exist before training cutoff. GPT models retrain every ~3-6 months. Plan content publication to precede expected training runs

### Calculations
- Training Data Survival Rate: Raw Common Crawl pages undergo multi-stage filtering: language detection, perplexity scoring (KenLM trained on Wikipedia), classifier-based quality filtering, deduplication (MinHash), toxicity removal. Only 1-5% of raw crawl survives. The survival rate depends on content quality, domain authority, language quality, and similarity to Wikipedia. Affects: Determines which content enters training data. Write like Wikipedia, be on well-connected domains, avoid toxicity/spam patterns.
- Citation Probability by Source Type: Different content sources have different citation rates in LLM outputs. Reddit dominates (40%), followed by Wikipedia (26%), YouTube (23%). For commercial queries, 64% of citations come from third-party 'Best Of' lists, not brand websites. Third-party mentions are 3x more correlated with AI visibility than brand-owned content. Affects: Prioritize Reddit presence, Wikipedia notability, and third-party list placement over brand website content.

### Training Data Influence Pipeline
- Content Publication: Brand creates or earns content across authoritative sources: Wikipedia, Reddit, news, academic papers, GitHub. Content must exist before training cutoff.
- Common Crawl Ingestion: Web crawlers archive content. Harmonic Centrality prioritizes well-connected domains. Content enters raw Common Crawl archive.
- Quality Filtering: Multi-stage filtering: language detection, perplexity scoring (Wikipedia proxy), classifier-based quality scoring, toxicity removal. 1-5% survival rate.
- Deduplication: MinHash, exact hash, suffix array dedup. 30-50% of surviving content removed as duplicates. Unique, original content survives.
- Dataset Assembly: Filtered content mixed with curated sources (Wikipedia upsampled 5x+, Reddit, books, code). Training dataset assembled (1T-15T+ tokens).
- Model Training: Next-token prediction over assembled dataset. Months of compute. Content associations become permanent model weights.
- Knowledge Encoding: Brand associations, entity knowledge, recommendation patterns encoded. Persist until next model version. Cannot be changed without retraining.

### Timeline
- 2020 (event): The Pile Released - EleutherAI releases 825 GB diverse dataset including Wikipedia, Reddit, Stack Exchange, ArXiv, PubMed, GitHub. Establishes multi-source training standard.
- 2023 (event): LLaMA Training Data Revealed - Meta publishes LLaMA recipe: 67% Common Crawl, 15% C4, 4.5% each Wikipedia/GitHub/Books, 2.5% ArXiv, 2% Stack Exchange. Template for open-source training.
- 2024 (event): Reddit-Google $60M Deal - Google licenses Reddit data for training. OpenAI also licenses. Reddit becomes most-cited LLM source. Platform data becomes premium training input.
- 2025 (event): Semrush Reddit Citation Study - 248K Reddit posts analyzed. 80% of cited posts have <20 upvotes. Q&A threads = 50%+ of citations. Topical alignment > engagement signals.
- 2025 (event): GEO Academic Framework - Princeton/IIT publish Generative Engine Optimization at KDD. Cite Sources (+40% visibility), Statistics Addition (+15-30%). Lower-ranked sites benefit most (+115%).
- 2026 (event): AI Visibility Tooling Matures - Profound, Semrush AI Visibility, Peec AI, Sight AI enable tracking brand mentions across ChatGPT, Claude, Gemini, Perplexity.

### Contradictions
- Public (AI companies (data quality claims)): We carefully curate training data for quality and accuracy. vs Internal (Common Crawl filtering reality): The dominant source is Common Crawl: internet-scale scraped content filtered through automated perplexity scoring and classifiers. 'Curation' is primarily automated, not human review. Quality proxy is similarity to Wikipedia, not factual accuracy. Medium-quality content passes through in vast quantities. Reddit with 5 upvotes qualifies as Tier 2 training data.
- Public (AI companies (neutrality claims)): Our AI provides unbiased, objective information and recommendations. vs Internal (Training data frequency bias + source weighting): Brands with more training data presence have higher token probability and are recommended more often. Wikipedia entries create entity recognition advantages. Reddit mentions in Q&A threads directly influence recommendations. The training data reflects the biases of its sources: Reddit's demographics, Wikipedia's coverage gaps, and Common Crawl's web-centrism. Objectivity is structurally impossible when knowledge comes from biased sources.

### Overview
LLM knowledge comes from training data: Common Crawl (dominant source, 67% of LLaMA), Wikipedia (most authoritative, upsampled 5x+), Reddit (40% of all LLM citations, $60M/year deal with Google), books, code, and academic papers. Only 1-5% of raw Common Crawl survives quality filtering, which uses Wikipedia as its quality proxy. Reddit posts with as few as 5-8 upvotes get cited. Third-party mentions in news outlets are 3x more correlated with AI visibility than brand-owned content. The implication: being present across authoritative sources before the training cutoff date determines what the model 'knows' about your brand.
