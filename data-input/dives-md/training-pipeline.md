# LLM Training Pipeline: From Web Crawl to Language Model

## Key Insight
The quality of training data matters more than model size. Chinchilla (Hoffmann et al., 2022) proved that a 70B model trained on 1.4T tokens outperforms a 280B model trained on 300B tokens at the same compute budget. The optimal ratio is ~20 tokens per parameter. Modern models massively over-train beyond this: LLaMA 3 (8B) trained on 15T tokens (~1,875 tokens/param) because smaller, over-trained models are cheaper to serve. The practical implication for LLM influence: what's IN the training data determines what the model knows. Common Crawl filtering uses Wikipedia as the quality proxy, meaning content that resembles Wikipedia's style and structure is more likely to survive into training data.

### Pre-Training Data Sources (patent)
- Common Crawl: ~250B pages, ~3.4 PB compressed. Only 1-5% survives filtering. Quality proxy: similarity to Wikipedia prose. 67% of LLaMA training data. Derivatives: C4 (750 GB, T5), RefinedWeb (5T tokens, Falcon), FineWeb (15T tokens, HuggingFace)
- Wikipedia: ~4B words (English), ~22 GB. Upsampled 3-10x vs natural proportion. LLaMA: Wikipedia repeated ~5 epochs vs ~1 for Common Crawl. Anchor for factual recall. Also serves as quality proxy for perplexity-based filtering of other sources
- Books: Books3 (~196K books, ~100 GB, from shadow library Bibliotik). Part of The Pile. Copyright lawsuits: Silverman v. OpenAI, Kadrey v. Meta, NYT v. OpenAI. Gutenberg (~60K public domain books)
- Code: GitHub (100M+ repos), Stack Overflow (50M+ Q&A). 5-15% of training mix. Dramatically improves reasoning and structured output. The Stack: 3 TB, 30+ languages. Licensing concerns: trained regardless of license
- Reddit: ~600 GB compressed (Pushshift archive). Reddit signed $60M/year deal with Google (Feb 2024). ~15% of GPT-3 training data. Conversational style, upvotes as quality proxy. Now behind API paywall
- Academic papers: S2ORC (81M+), arXiv (2.4M+), PubMed (36M+). 2-5% of training mix. Critical for technical knowledge and citation patterns

### Data Filtering & Deduplication (patent)
- Perplexity filtering: Train KenLM (5-gram) on Wikipedia. Score each document. High perplexity = unlike Wikipedia = likely low quality. GPT-3 used logistic regression trained on Reddit-upvoted pages vs random crawl
- Classifier-based: Binary classifier (fastText or small transformer) on 'known good' (Wikipedia, books) vs 'known bad' (random web). LLaMA used fastText embeddings with linear classifier
- MinHash deduplication: Compute MinHash signatures over n-gram shingles. LSH finds candidate duplicate pairs in sub-linear time. Typical: 128 hash functions, 5-gram shingles, Jaccard threshold 0.8. Common Crawl has 30-50% near-duplicates
- Toxicity filtering: Perspective API (Google/Jigsaw), custom classifiers, word blocklists, domain blocklists (UT1, Shallalist). Over-filtering removes legitimate sensitive content
- PII removal: Regex for emails, phones, SSNs. NER for names. Imperfect: models still memorize some PII
- Impact: Dedup consistently improves quality. Lee et al. (Google, 2022): exact dedup removes ~30% tokens, near-dedup another ~20%, with equal or better performance

### Key People (leak)
- Andrej Karpathy: Built GPT training infrastructure at OpenAI. Former Director of AI at Tesla (Autopilot). Created nanoGPT, minbpe, llm.c. Founded Eureka Labs (2024, AI education). PhD Stanford under Fei-Fei Li
- Ilya Sutskever: Co-founder/Chief Scientist OpenAI (2015-2024). Co-authored AlexNet (2012). Key scaling hypothesis advocate. Led board ouster of Altman (Nov 2023), reversed. Left Jun 2024. Co-founded Safe Superintelligence Inc. (SSI, $1B raised, $5B valuation)
- Dario Amodei: Co-founder/CEO Anthropic. Former VP Research OpenAI (led GPT-2, GPT-3). Left over safety/commercialization disagreements. PhD computational neuroscience, Princeton
- Daniela Amodei: Co-founder/President Anthropic. Former VP Operations OpenAI. Led Anthropic fundraising ($7.6B+). Previously Stripe (finance), US Congress
- Jeff Dean: Google Chief Scientist. Co-designed MapReduce, BigTable, Spanner, TensorFlow, TPUs, BERT, PaLM, Gemini. 200+ papers. Most impactful individual engineer in Google history
- Noam Shazeer: Co-invented Transformer (2017), Switch Transformer (MoE). Co-founded Character.AI (2021). Google acqui-hired back for $2.7B (Aug 2024). Now leads Gemini at Google DeepMind

### Open Source Ecosystem (exploit)
- LLaMA/Llama (Meta): 7B-405B. Llama 3: 15T+ tokens, tiktoken, 128K context. Spawned Alpaca, Vicuna, WizardLM. Democratized LLM research. Hugo Touvron led
- Mistral (Paris): Founded May 2023 by Mensch, Lample, Lacroix (ex-Meta/DeepMind). 7B matches LLaMA 2 13B. Mixtral 8x7B pioneered open MoE. ~$6B valuation. Fastest European unicorn
- DeepSeek (Hangzhou): V3: 671B MoE, $5.6M training, 2048 H800s. R1: reasoning via pure RL, open weights, competitive with o1. Proved frontier doesn't require billion-dollar budgets. Triggered NVIDIA stock drop Jan 2025
- Falcon (TII, Abu Dhabi): 40B/180B. RefinedWeb dataset proved web-only data works with aggressive dedup. Apache 2.0
- Gemma (Google): 2B-27B. Knowledge distilled from Gemini. 27B matches LLaMA 3 70B on some tasks at fraction of inference cost
- Qwen (Alibaba): 0.5B-72B. 18T+ tokens. Strong multilingual (Chinese-English). Apache 2.0
- OLMo (AI2): Only truly open model: data + code + weights + training logs. Research transparency standard

### Calculations
- Chinchilla Scaling Law: Trained 400+ models from 70M to 16B params on 5B to 500B tokens. Found optimal ratio: ~20 tokens per parameter. Chinchilla (70B params, 1.4T tokens) outperformed Gopher (280B params, 300B tokens) at same compute. For compute-optimal training, scale parameters and data equally. Affects: Determines optimal model size vs training data trade-off. Labs now intentionally over-train for inference efficiency.
- Kaplan Scaling Laws: Loss scales as power law with compute, data, and parameters. Given 10x compute increase, allocate ~5.5x to model size, ~1.8x to data. Led to GPT-3 philosophy: big model, less data. Later superseded by Chinchilla's data-scaling insight. Affects: Explains predictable improvement with scale. Budget allocation: how to split compute between model size and data.
- Training Cost Model: Training cost depends on model parameters, training tokens, hardware efficiency, and infrastructure. C ~ 6*N*D gives total FLOPs. Divide by hardware throughput to get GPU-hours. Multiply by cost per GPU-hour. Affects: Determines who can train frontier models. Architectural innovation (MoE, FP8) dramatically reduces costs.

### LLM Training Pipeline
- Data Collection: Web crawls (Common Crawl), curated sources (Wikipedia, books, code, academic papers, Reddit). Terabytes of raw text.
- Data Cleaning: Language detection (fastText), quality filtering (perplexity/classifier), PII removal, toxicity filtering, heuristic rules. Only 1-5% of raw crawl survives.
- Deduplication: URL-level, exact hash (SHA-256), MinHash/LSH near-dedup (Jaccard 0.8), suffix array substring dedup. Removes 30-50% of remaining data.
- Tokenization: BPE (GPT, LLaMA 3), SentencePiece (LLaMA 1-2), tiktoken. Vocabulary sizes: 32K-128K tokens. Token boundaries affect model understanding.
- Pre-Training: Next-token prediction (decoder) or masked LM (encoder). Months of GPU/TPU compute. Cosine learning rate schedule, warmup, gradient clipping. Distributed across thousands of GPUs.
- Supervised Fine-Tuning (SFT): Train on human-written instruction/response pairs. Typically 10K-100K examples. Transforms base model into instruction-follower.
- RLHF/RLAIF: Train reward model on human preference comparisons. Optimize policy via PPO or DPO. Constitutional AI (Anthropic) uses AI-generated feedback.
- Evaluation: MMLU, HumanEval, GSM8K, HellaSwag, ARC, TruthfulQA, MT-Bench, Chatbot Arena (LMSYS). Red-teaming for safety.

### Timeline
- 2017 (patent): Transformer Published - Vaswani et al. publish 'Attention Is All You Need.' Foundation for all modern LLMs.
- 2018 (patent): GPT-1 + BERT - Two pre-training paradigms: GPT (decoder, causal) and BERT (encoder, masked). Both Transformers.
- 2020 (patent): GPT-3 + Scaling Laws - 175B params, few-shot learning. Kaplan scaling laws show predictable improvement with compute/data/params.
- 2020 (patent): The Pile Released - EleutherAI releases 825 GB diverse training dataset. Enables open-source model training.
- 2022 (patent): Chinchilla Scaling - Hoffmann et al.: optimal 20 tokens per parameter. Shifts field toward data scaling. Directly influenced LLaMA.
- 2022 (event): ChatGPT Launch - GPT-3.5 + RLHF. Fastest to 100M users. Triggers global AI race.
- 2023 (event): LLaMA Democratizes LLMs - Meta releases LLaMA. Weights leak. Proves small models + more data compete with larger models.
- 2024 (event): DeepSeek V3: $5.6M Frontier - 671B MoE trained for $5.6M on 2048 H800s. FP8 training. Challenges billion-dollar training narrative.
- 2025 (event): Open Ecosystem Matures - Llama, Mistral, Qwen, DeepSeek, Gemma form competitive open ecosystem. Gap with closed models narrows to months.

### Contradictions
- Public (AI companies (data sourcing)): We train on publicly available data from the internet. vs Internal (Training data composition: Books3, licensed Reddit, scraped code): Labs use copyrighted books (Books3 from shadow library), paywalled content, licensed Reddit data ($60M/year to Google), and scraped GitHub code regardless of license. 'Publicly available' conflates 'accessible' with 'legally usable.' NYT v. OpenAI and multiple author lawsuits challenge this framing.
- Public (AI companies (openness claims)): Our model is open source. vs Internal (Open weights vs true open source): Most 'open' models release weights but not training data, curation code, RLHF data, or full methodology. Only AI2's OLMo provides data + code + weights + training logs. 'Open weights' is not 'open source.' The training data (the most valuable component) remains proprietary even in 'open' releases.
- Public (AI industry narrative): Training frontier models requires billions of dollars and massive infrastructure. Only a few labs can compete. vs Internal (DeepSeek V3: $5.6M training cost): DeepSeek V3 trained a frontier 671B MoE for ~$5.6M using architectural innovation (MoE, FP8, efficient attention) on 2048 H800 GPUs. Mistral built competitive models with a small European team. Chinchilla showed that model size matters less than data volume. The barrier is algorithmic innovation, not raw capital.

### Overview
Training an LLM requires three inputs: data (trillions of tokens from Common Crawl, Wikipedia, books, code, Reddit), compute (thousands of GPUs/TPUs costing $5M-$100M+), and algorithms (next-token prediction with scaling laws). The pipeline spans data collection, filtering, deduplication, tokenization, distributed training, and evaluation. Common Crawl is the dominant source (67% of LLaMA training data), but only 1-5% of raw crawl survives quality filtering. Wikipedia is massively upsampled (5x+ its natural proportion) as a factual anchor. DeepSeek V3 proved frontier models can train for ~$5.6M, challenging the narrative that only billion-dollar labs can compete.
