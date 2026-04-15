# Transformer Architecture: The Foundation of All Modern LLMs

## Key Insight
The Transformer's core innovation is self-attention: each token in a sequence attends to every other token simultaneously, unlike RNNs which process tokens one at a time. The formula Attention(Q,K,V) = softmax(QK^T / sqrt(d_k))V computes relevance between all token pairs in one step. Multi-head attention (8 heads in the original paper) lets different heads specialize in different relationship types (syntax, semantics, proximity). The key trade-off: attention is O(n^2) in sequence length, making long contexts expensive. Modern solutions (FlashAttention, RoPE, Ring Attention) mitigate this but don't eliminate it.

### Architecture Components (patent)
- Self-Attention: Three learned matrices (W_Q, W_K, W_V) project each input token into Query, Key, and Value vectors. Queries compare against all Keys to produce attention weights; weights applied to Values produce output. Every token sees every other token in one step
- Multi-Head Attention: h parallel attention heads (8 in original, 32-128 in modern models). Each head uses different W_Q, W_K, W_V projections, learning to capture different relationship types. Outputs concatenated and projected: MultiHead = Concat(head_1,...,head_h)W^O
- Positional Encoding: Transformers have no inherent notion of token order. Original paper uses sinusoidal encoding: PE(pos,2i) = sin(pos/10000^(2i/d)). Modern models use Rotary Position Embeddings (RoPE) or learned positional embeddings
- Feed-Forward Network (FFN): Two linear transformations with activation. FFN(x) = max(0, xW1+b1)W2+b2. Inner dimension typically 4x model dimension. This is where 'knowledge storage' happens (attention routes, FFN stores)
- Layer Normalization: Normalizes activations. Original: post-norm (after attention/FFN). Modern: pre-norm (before), more stable for deep models
- Residual Connections: Each sub-layer adds input to output: x + Sublayer(x). Prevents vanishing gradients in deep networks. Critical for training models with 100+ layers

### Architecture Variants (patent)
- Encoder-only (BERT): Bidirectional attention (every token sees every other token). Pre-trained with Masked Language Modeling (mask 15%, predict). Best for: classification, embeddings, extractive QA. Used in Google Search (DeepRank, RankEmbed)
- Decoder-only (GPT, Claude, LLaMA): Causal/unidirectional attention (each token only sees tokens before it). Pre-trained with next-token prediction. Best for: text generation, chat, code. Dominant architecture for modern LLMs
- Encoder-Decoder (T5): Encoder processes input bidirectionally, decoder generates output autoregressively with cross-attention. Pre-trained with span corruption. Best for: translation, summarization
- Mixture-of-Experts (MoE): Multiple FFN 'experts', only a subset activated per token. GPT-4 (rumored ~1.8T total, much less active), Mixtral 8x7B, DeepSeek V3 (671B total, 37B active). Enables larger model capacity at same inference cost

### Key People: The 8 Transformer Authors (leak)
- Ashish Vaswani (first author): Co-founder & CEO, Essential AI. Previously co-founded Adept AI. Left Google 2021
- Noam Shazeer: Co-founded Character.AI (2021). Google acqui-hired him back for $2.7B (Aug 2024). Now leads Gemini model development at Google DeepMind. Also co-invented Switch Transformer (MoE)
- Niki Parmar: Joined Anthropic Dec 2024. Previously co-founded Adept AI and Essential AI. Contributed to Claude 3.7 Sonnet
- Jakob Uszkoreit: Co-founder & CEO, Inceptive (biotech, RNA drug design using ML)
- Llion Jones: Co-founder & CTO, Sakana AI (Tokyo, nature-inspired AI)
- Aidan Gomez: Co-founder & CEO, Cohere (enterprise NLP, ~$5.5B valuation). Was an intern during the paper
- Lukasz Kaiser: Member of Technical Staff, OpenAI. Led core R&D on GPT-4, GPT-5, o1, o3
- Illia Polosukhin: Co-founder, NEAR Protocol (decentralized AI). Left Google same year as the paper (2017)
- All 8 have left Google. 7 founded startups. 7 reunited at NVIDIA GTC Mar 2024 with Jensen Huang

### Model Families and Context Windows (exploit)
- GPT series (OpenAI): GPT-1 (117M, 2018), GPT-2 (1.5B, 2019), GPT-3 (175B, 2020), GPT-4 (~1.8T MoE, 2023), GPT-4o (~200B, 2024). Decoder-only, autoregressive
- Claude (Anthropic): Claude 3 (Haiku/Sonnet/Opus, 2024), Claude 3.5 Sonnet, Claude 4/4.5 (2025). Constitutional AI (RLAIF). Up to 200K token context
- Gemini (Google): Natively multimodal. Gemini 1.5 Pro: 1M+ token context via MoE + specialized attention
- LLaMA (Meta): 7B-405B, open weights. Switched from SentencePiece to tiktoken in LLaMA 3. Trained on 15T+ tokens. Spawned thousands of fine-tunes
- Mistral (Paris): 7B matches LLaMA 2 13B. Mixtral 8x7B pioneered open MoE. Founded by ex-Meta/DeepMind researchers
- DeepSeek (China): V3 (671B MoE, $5.6M training cost). R1 (reasoning via pure RL). Proved frontier models don't require billion-dollar budgets
- Context window costs: O(n^2) attention means 512 tokens ~0.07 GB, 8K tokens ~18 GB, 32K tokens ~280 GB. FlashAttention mitigates via tiling. Current limits: GPT-4o 128K, Claude 200K, Gemini 1M+

### Calculations
- Self-Attention Formula: Each token produces Query, Key, Value vectors by multiplying its embedding by learned weight matrices. Attention scores computed by dot product of queries with all keys, scaled by sqrt(d_k) to prevent softmax saturation. Scores normalized via softmax to probabilities. Probabilities weight the values to produce output. Affects: Foundation of all LLMs. Every token attends to every other token in one step.
- Multi-Head Attention: Instead of one attention function, h parallel attention heads each operate on different learned projections. Each head captures different relationship types (syntax, semantics, proximity). Outputs are concatenated and linearly projected. Affects: More heads = richer representations. GQA (Grouped Query Attention) in LLaMA 2+ reduces KV cache for efficient inference.
- Quadratic Attention Cost: Self-attention computes an n x n matrix where n = sequence length. Time complexity O(n^2 * d), memory O(n^2). This quadratic scaling is the fundamental bottleneck for long contexts. FlashAttention uses tiling to avoid materializing the full matrix in GPU memory. Sparse/linear attention approximations trade accuracy for efficiency. Affects: Determines context window limits. Longer context = quadratically more compute. Trade-off: context size vs cost.

### Transformer Forward Pass
- Tokenization: Input text split into tokens via BPE (GPT, LLaMA 3), SentencePiece (LLaMA 1-2), or tiktoken. Each token mapped to an embedding vector.
- Positional Encoding: Position information added to embeddings. Sinusoidal (original), learned (GPT), or RoPE (LLaMA, modern models).
- Self-Attention: Each token attends to all other tokens (encoder) or preceding tokens only (decoder). Q, K, V projections computed per head.
- Feed-Forward Network: Two linear transformations with activation (ReLU, GeLU, SwiGLU). Inner dimension 4x model dimension. Knowledge storage layer.
- Layer Stacking: Attention + FFN repeated N times (12 layers in BERT-base, 96+ in frontier models). Residual connections and layer normalization at each layer.
- Output Projection: Final hidden states projected to vocabulary size. Softmax produces probability distribution over next token (decoder) or masked token (encoder).

### Timeline
- 2014 (patent): Sequence-to-Sequence (Sutskever et al.) - Encoder-decoder RNNs with attention for translation. Foundation that Transformers would replace.
- 2015 (patent): Bahdanau Attention - Additive attention mechanism for RNN seq2seq. Proved attention works; Transformers would make it the entire architecture.
- 2017 (patent): Attention Is All You Need - Vaswani, Shazeer et al. publish the Transformer. Replaces recurrence entirely with self-attention. 8 authors, all now outside Google.
- 2018 (patent): BERT + GPT-1 - Two competing pre-training paradigms: BERT (encoder, bidirectional) and GPT (decoder, causal). Both are Transformers.
- 2020 (patent): GPT-3: Scale Changes Everything - 175B parameters demonstrate few-shot learning without fine-tuning. Proves scaling Transformers creates emergent capabilities.
- 2023 (event): GPT-4 + MoE - Rumored Mixture-of-Experts architecture. Multimodal. Sets new frontier. MoE becomes dominant architecture for efficiency.
- 2024 (event): FlashAttention + 1M Context - FlashAttention v2/v3 enables practical long-context models. Gemini 1.5 Pro reaches 1M tokens. Ring Attention enables distributed long-context.
- 2025 (event): DeepSeek R1: RL-Trained Reasoning - Pure reinforcement learning produces chain-of-thought reasoning without supervised examples. Open weights. Proves Transformers + RL = reasoning capability.

### Contradictions
- Public (AI industry marketing): AI understands and reasons about text like humans do. LLMs 'understand' language. vs Internal (Transformer architecture: next-token prediction): Transformers predict the most likely next token given preceding context. There is no semantic 'understanding' in the architecture. Self-attention computes statistical relationships between token positions. The appearance of understanding emerges from pattern matching at scale, not from any reasoning mechanism. The architecture has no world model, memory, or causal reasoning beyond what statistical patterns in training data provide.
- Public (AI companies (marketing larger context windows)): Our model supports 1 million token context windows. You can input entire codebases or books. vs Internal (Quadratic attention cost + practical performance degradation): Attention is O(n^2). A 1M token context requires distributed computation across many GPUs and costs orders of magnitude more per query than short contexts. Performance on 'needle-in-a-haystack' tasks degrades in the middle of very long contexts ('lost in the middle' phenomenon). Supporting a context length and effectively using it are different things.

### Overview
The Transformer architecture (Vaswani et al., 2017, 'Attention Is All You Need') replaced recurrent neural networks with self-attention, enabling massive parallelization and capturing long-range dependencies in O(1) operations. All modern LLMs (GPT, Claude, Gemini, LLaMA, Mistral) are Transformers. All 8 original authors have left Google: Shazeer (Character.AI, back at Google), Vaswani (Essential AI), Parmar (Anthropic), Uszkoreit (Inceptive), Jones (Sakana AI), Gomez (Cohere), Kaiser (OpenAI), Polosukhin (NEAR Protocol).
