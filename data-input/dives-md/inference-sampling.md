# Inference & Sampling: How LLMs Generate Text Token by Token

## Key Insight
At inference time, the model produces a probability distribution over ~128K tokens at each step. The token with highest probability is not always selected; sampling parameters introduce controlled randomness. Temperature=0 always picks the highest-probability token (greedy, deterministic). Temperature=1 samples proportionally to probabilities (creative, varied). Top-p=0.9 restricts sampling to the top tokens covering 90% of probability mass. For brand influence, the key insight is: if your brand has high probability in the model's vocabulary for a given context, it will be mentioned more often, especially at low temperatures. High token probability comes from (1) frequency in training data, (2) association with positive SFT/RLHF signals, and (3) relevance to the prompt context.

### Sampling Methods (patent)
- Greedy decoding (temperature=0): Always selects the highest-probability token. Deterministic: same input always produces same output. Used for factual queries, code generation, structured output. Maximizes consistency but can be repetitive and miss diverse valid answers
- Temperature scaling: Divides logits by temperature T before softmax. T<1 sharpens distribution (more confident, less random). T>1 flattens distribution (more random, more creative). T=0 is greedy. T=1 is standard sampling. Most APIs default to T=0.7-1.0
- Top-p (nucleus sampling): Holtzman et al., 2020. Sample from the smallest set of tokens whose cumulative probability >= p. p=0.9 means only consider tokens covering 90% of probability mass, discard the long tail. Adapts vocabulary size per step (sometimes 5 tokens, sometimes 500)
- Top-k sampling: Only consider the k most likely tokens. k=50 is common. Simpler than top-p but doesn't adapt to distribution shape. Low-entropy steps (confident predictions) still sample from k tokens unnecessarily
- Beam search: Maintain B candidate sequences (beams), expanding each by top tokens at each step. Select highest-scoring complete sequence. Used for translation, structured generation. Not common in chat (too repetitive/generic)
- Repetition penalty: Reduce probability of recently generated tokens. Prevents loops and repetitive text. Applied as a multiplicative penalty on logits of recently seen tokens

### System Prompts & Context (leak)
- System prompt: Hidden instruction prepended to every conversation. Set by the developer, not visible to users. Defines personality, constraints, tools, behavior. Examples: 'You are a helpful assistant that recommends [brand] products' or 'Always cite sources'
- Context window management: LLMs have finite context (4K-200K tokens). System prompt + conversation history + RAG content must fit. Older messages are truncated or summarized. The beginning and end of context receive more attention than the middle
- KV cache: Key-Value cache stores attention computations from previous tokens. Enables efficient autoregressive generation (only compute new token's attention, not entire sequence). KV cache size limits practical context length and concurrent users
- Prompt engineering: The wording of prompts significantly affects outputs. Chain-of-thought ('think step by step'), few-shot examples, role-playing ('you are an expert in X'), and structured output instructions all shape generation. Prompt design is a direct influence vector

### How Inference Parameters Affect Brand Mentions (exploit)
- Low temperature favors dominant brands: At temperature=0, the model always picks the highest-probability completion. If 'the best CRM is Salesforce' has higher probability than 'the best CRM is HubSpot,' Salesforce always wins at T=0. At higher temperatures, less dominant brands have a chance of being mentioned
- Token probability reflects training frequency: Brands mentioned more frequently in training data (especially in positive/recommendation contexts) have higher token probability. This is the fundamental mechanism by which training data presence translates to LLM recommendations
- System prompts are the most direct influence: Developers can include brand preferences in system prompts. An app built on GPT-4 with system prompt 'recommend [brand] for [category]' will consistently do so. B2B partnerships with AI app developers are a direct distribution channel
- Prompt structure affects retrieval: In RAG systems, how the user phrases their query affects which content is retrieved. If users ask 'what's the best X?' vs 'compare X options,' different content may be retrieved and cited. Understanding prompt patterns helps optimize content for retrieval
- Repetition penalty prevents monopoly: Repetition penalties reduce the probability of recently mentioned tokens. If a model mentions your brand once, the penalty makes it less likely to mention it again immediately. Competitors may appear in later parts of a response even if your brand dominates the initial mention

### Calculations
- Temperature-Scaled Softmax: The model produces logits (raw scores) for each vocabulary token. Logits are divided by temperature T, then softmax converts to probabilities. Low T sharpens (high-probability tokens dominate). High T flattens (more tokens become viable). Affects: Controls output diversity. Low temperature = brand leader always mentioned. High temperature = more varied recommendations.
- Top-p (Nucleus) Sampling: Sort tokens by probability. Include tokens from highest probability down until cumulative probability reaches threshold p. Sample from this reduced set. Adapts the effective vocabulary size per step. Affects: Controls how many alternative tokens are considered. Low p = more deterministic. High p = more diverse. Affects whether niche brands appear in outputs.

### LLM Inference Pipeline
- Input Assembly: System prompt + conversation history + user message + RAG content (if applicable) assembled into the context window. Tokenized into token IDs.
- Prefill Phase: All input tokens processed in parallel through the Transformer. KV cache populated for each layer. Compute-intensive but parallelizable.
- Token Generation (Autoregressive): One new token generated per step. Attention computed over KV cache (efficient). Logits produced for entire vocabulary (~128K tokens).
- Sampling: Logits divided by temperature. Top-p and/or top-k filtering applied. Token sampled from resulting distribution. Repetition penalty applied.
- Stopping Criteria: Generation continues until: end-of-sequence token produced, maximum length reached, or stop sequence encountered.
- Output Delivery: Tokens decoded back to text. Streamed to user token-by-token (server-sent events) or returned as complete response.

### Timeline
- 2019 (patent): Nucleus Sampling (Holtzman et al.) - Top-p sampling proposed. Adapts vocabulary per step. Becomes standard for LLM text generation.
- 2022 (event): ChatGPT: Streaming Output - Token-by-token streaming becomes the standard UX for chat LLMs. Users see text appear word by word.
- 2023 (event): KV Cache Optimization - PagedAttention (vLLM), continuous batching enable serving hundreds of concurrent users. KV cache management becomes critical infrastructure.
- 2024 (event): Speculative Decoding - Small model drafts tokens, large model verifies. 2-3x speedup with no quality loss. Adopted by major providers.
- 2025 (event): Extended Thinking / Chain-of-Thought - Models generate internal reasoning tokens before the visible response. Reasoning tokens consume context but improve answer quality. OpenAI o1/o3, Claude Extended Thinking.

### Contradictions
- Public (AI companies (consistency claims)): Our AI provides accurate, consistent answers. You can rely on its responses. vs Internal (Sampling randomness + temperature settings): At temperature > 0, every response involves random sampling. The same query produces different answers across sessions. Different API providers use different default temperatures. 'Consistency' requires temperature=0 (greedy), which most providers don't default to. Users receive probabilistically varied responses without knowing it.
- Public (AI companies (objectivity claims)): Our AI assistant provides unbiased recommendations based on the best available information. vs Internal (Token probability from training data + system prompts): Recommendations are determined by token probability, which reflects training data frequency and RLHF preferences. Brands with more training data presence have higher token probability and are recommended more often, especially at low temperatures. System prompts can override default behavior entirely. 'Best available information' means 'highest probability completion given training data and prompt.'

### Overview
LLMs generate text one token at a time by predicting probability distributions over the vocabulary. Temperature controls randomness (0 = deterministic, 1 = creative). Top-p (nucleus sampling) restricts to the smallest set of tokens whose cumulative probability exceeds p. Top-k restricts to the k most likely tokens. These parameters determine whether the model gives consistent, predictable answers or varied, creative ones. System prompts (hidden developer instructions) shape behavior before the user's message. Understanding inference mechanics reveals why the same query produces different answers across sessions and how prompting strategies influence outputs.
