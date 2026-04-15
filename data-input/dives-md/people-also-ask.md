# People Also Ask: Related Question Generation Engine

## Key Insight
PAA operates as a closed-loop question generation system where each user interaction feeds back into the model. When a user clicks a PAA question, the system generates new related questions based on the clicked question, not the original query. This creates a branching exploration tree rooted in the original query but expanding outward through semantic relationships. The question generation engine (US9213748, US9679027) scores candidate questions by relevance to the seed query, diversity from already-shown questions, and answer availability in the index. Answer extraction uses passage-level ranking to select the best snippet from a candidate page, then attributes it with a source link. SuperRoot decides whether to trigger PAA based on query classification, and Tangram determines its SERP position relative to ads, organic results, and other features.

### API Leak Evidence (leak)
- Tangram controls PAA box placement on the SERP. PAA competes for position with other SERP features (knowledge panels, featured snippets, video carousels) through Tangram's layout engine.
- SuperRoot orchestrates which features trigger per query. PAA activation depends on query classification: informational queries trigger PAA, navigational and transactional queries suppress it.
- Query understanding classifiers determine intent type and topical category, feeding into SuperRoot's decision to show PAA. Queries classified as ambiguous or broad receive more PAA questions.
- Passage-level indexing signals support answer extraction. The API leak reveals passage-level scoring attributes used to identify the best answer span within a document.
- Note: PAA does not have dedicated API leak fields. The signals driving PAA come from the SuperRoot/Tangram orchestration layer and query classification pipeline, not a standalone PAA module.

### 4 Patents (patent)
- Question answering to populate knowledge base (US10108700): Extracts question-answer pairs from web documents to build a structured QA knowledge base. Candidate answers are scored by passage relevance, source authority, and answer type match. This knowledge base feeds PAA with pre-extracted answers.
- Semi-structured question answering (US10346485): Handles questions that fall between factoid and open-ended by combining structured data (Knowledge Graph) with unstructured web passages. Enables PAA to answer questions that require synthesizing multiple sources.
- Generating related questions for search queries (US9213748): Core question generation patent. Given a seed query, generates candidate related questions by analyzing query logs, co-occurrence patterns, and semantic similarity. Candidate questions ranked by relevance, diversity, and predicted click probability.
- Generating related questions (continuation) (US9679027): Extends the question generation framework with iterative expansion. When a user clicks a PAA question, the system generates new questions seeded from the clicked question, creating the branching exploration behavior observed in production.

### How PAA Affects Your SEO (exploit)
- Target PAA questions directly: Identify PAA questions for your target keywords and create content that answers them concisely in typically short, direct-answer passages within the first paragraph, followed by deeper elaboration. Google extracts passage-level snippets, so the answer must be self-contained.
- Use question-based headings: Structure content with H2/H3 headings that match PAA question phrasing exactly. The answer extraction pipeline favors content where the question appears as a heading and the answer follows immediately below.
- PAA absorbs organic clicks: SERP features like PAA occupy significant viewport real estate and absorb clicks that would otherwise reach organic results. If your page ranks #1 but a competitor's page appears in PAA above you, users may never scroll to your result. Monitor PAA presence for your keywords as aggressively as you monitor rankings.
- Optimize for the branching tree: PAA questions expand dynamically. Getting into the initial PAA set matters most, but secondary questions (generated when users click) represent additional visibility. Create comprehensive topic clusters that can answer the full question tree around a topic.
- Schema markup signals answer structure: FAQ schema, HowTo schema, and QAPage schema help Google identify question-answer pairs in your content for extraction into PAA boxes.
- Source authority matters: Patent US10108700 scores answers partly by source authority. High-authority domains are disproportionately selected as PAA answer sources. Build topical authority before expecting PAA placement.

### System Context from DOJ Trial (doj)
- SERP features and competition: Judge Mehta's opinion in the DOJ antitrust case discusses how SERP composition affects competition. SERP features (including PAA, knowledge panels, and featured snippets) occupy significant real estate, reducing the visibility of organic results.
- SERP real estate allocation: The Mehta opinion addresses how Google's control over SERP layout, including which features appear and where, is central to the competitive dynamics of search. PAA boxes push organic results further below the fold as they expand.
- No PAA-specific DOJ evidence: No DOJ exhibits in this repository cite PAA by name. The above context comes from the broader SERP feature analysis in the Mehta opinion and general trial testimony about how Google-hosted answers reduce traffic to external sites.

### Calculations
- Related Question Scoring: For a given seed query, the system generates candidate related questions from query logs, search session co-occurrences, and semantic models. Each candidate question is scored on relevance to the seed query, diversity from other selected questions, and predicted answer availability. The top-N questions (typically 4) are selected for initial display. Affects: Affected by: query log co-occurrence frequency, semantic similarity model, answer coverage in index, diversity threshold between questions.
- Answer Extraction and Ranking: For each selected PAA question, the system retrieves candidate answer pages and extracts passage-level snippets. Each candidate answer is scored by passage relevance to the question, source page authority, answer type match (e.g., definition vs list vs explanation), and snippet quality (length, completeness, self-containedness). The top answer is displayed with source attribution. Affects: Affected by: passage-level relevance scoring, page authority, answer type classification, snippet extraction quality, structured data markup on source page.
- PAA Trigger Decision: SuperRoot decides whether to show PAA for a given query based on query classification from the query understanding pipeline. Informational queries with moderate-to-high ambiguity are the primary PAA triggers. Navigational queries, adult queries, and highly specific factoid queries (answered by Knowledge Graph) suppress PAA. The decision also factors in available SERP real estate after ads and other high-priority features. Affects: Affected by: query intent classification, query ambiguity score, SERP feature competition (ads, knowledge panel, featured snippet), available viewport space.

### People Also Ask in the Ranking Pipeline
- Query Understanding: Query classified by intent (informational, navigational, transactional), topical category, and ambiguity level. These signals determine whether PAA triggers.
- SuperRoot Feature Trigger: SuperRoot evaluates query classification and decides which SERP features to activate. PAA is triggered for informational queries exceeding the ambiguity threshold.
- Question Generation: Related question generation model produces candidate questions from query logs, session co-occurrences, and semantic models. Candidates scored by relevance, diversity, and answer availability (US9213748, US9679027).
- Answer Retrieval: For each selected question, candidate answer pages are retrieved from the index. Passage-level ranking identifies the best answer snippet from each candidate page.
- Answer Extraction: Best passage extracted as a short, direct-answer snippet. Source authority, answer type match, and snippet self-containedness scored. Top answer selected with source attribution (US10108700, US10346485).
- Tangram Placement: Tangram's layout engine determines PAA box position on the SERP, competing with ads, knowledge panels, featured snippets, and video carousels for viewport real estate.
- Dynamic Expansion: When a user clicks a PAA question, the system generates new related questions seeded from the clicked question (not the original query), creating a branching exploration tree served in real time.

### Timeline
- 2013 (patent): Related Question Generation (US9213748) - Google patents a system for generating related questions for search queries. Core algorithm scores candidates by relevance to seed query, diversity, and predicted click probability from query log analysis.
- 2013 (patent): QA Knowledge Base (US10108700) - Google patents question answering to populate knowledge base. Extracts QA pairs from web documents, scoring by passage relevance and source authority. Feeds PAA with pre-extracted, high-confidence answers.
- 2015 (patent): People Also Ask Launches - Google rolls out PAA boxes on desktop and mobile SERPs. Initially limited to 4 questions per query, with dynamic expansion on click.
- 2015 (patent): Iterative Question Expansion (US9679027) - Continuation patent extends question generation with iterative expansion. Clicking a question generates new questions seeded from the clicked question, enabling the branching PAA behavior.
- 2017 (patent): Semi-Structured QA (US10346485) - Patent for semi-structured question answering combines Knowledge Graph structured data with unstructured web passages. Enables PAA to handle questions requiring synthesis across multiple sources.
- 2023 (doj): DOJ Trial: SERP Feature Impact - Judge Mehta's opinion in the DOJ antitrust case discusses how SERP composition and feature placement affect competition. No PAA-specific exhibits exist, but the broader analysis of Google-hosted answers reducing traffic to external sites applies to PAA.

### Contradictions
- Public (Google Search Central Blog): People Also Ask helps users explore topics and find relevant information across the web. vs Internal (SERP feature composition (Mehta opinion, Tangram architecture)): PAA boxes expand dynamically and absorb clicks that would otherwise go to organic results. Each expanded question triggers more questions, creating an infinite scroll of Google-hosted answers extracted from publisher content.

### Overview
People Also Ask (PAA) is Google's dynamic question-answer SERP feature that surfaces semantically related questions to a user's query, each expandable to reveal an extracted answer snippet. Orchestrated by SuperRoot and placed by Tangram, PAA boxes appear for the majority of informational queries. The system relies on question generation models (4 patents), query understanding classifiers, and answer extraction pipelines to produce questions that anticipate user intent and pull answers from web pages. PAA boxes dynamically expand as users click, generating new related questions in real time.
