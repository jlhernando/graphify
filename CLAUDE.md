# Rankpedia Graph

Knowledge graph project built with graphify on top of Rankpedia's research corpus.

## Data
- `data-input/patents/` - 108 Google patents referenced in Rankpedia dive files
- `data-input/doj/exhibits/` - 42 DOJ antitrust trial exhibit transcripts
- `data-input/doj/*.json` - DOJ metadata (sources, signals, cross-references)
- `data-input/dives/` - 64 Rankpedia dive files (structured JSON research)

## Purpose
Benchmark graphify's knowledge graph against Rankpedia's manual curation to discover:
1. Connections between patents that manual curation missed
2. Cross-links between DOJ evidence and patents
3. Topic clusters among the 1595 unreferenced patents (future expansion)

## Running
```bash
source .venv/bin/activate
# Then use /graphify data-input
```

## Benchmarking
Compare graphify output against Rankpedia's existing structure:
- Are graphify's clusters aligned with Rankpedia's dive topics?
- Does graphify surface patent connections not in the dive files?
- What DOJ-patent links does graphify find vs what Rankpedia has?
