# Fine-Tuning & Alignment: How LLMs Learn to Follow Instructions and Refuse Harm

## Key Insight
Fine-tuning and alignment are where value judgments are encoded into LLMs. The pre-trained model has knowledge; alignment determines how it uses that knowledge. SFT data teaches the model what a 'good' response looks like (format, tone, helpfulness). RLHF reward models encode human preferences about which responses are better. Constitutional AI principles define what the model should and should not do. For LLM influence, the key vectors are: (1) being represented in SFT training data as a recommended entity, (2) being associated with positive sentiment in the preference data, and (3) not triggering refusal/safety classifiers that would block mention of your brand.

### Alignment Techniques (patent)
- Supervised Fine-Tuning (SFT): Train base model on human-written (instruction, response) pairs. Typically 10K-100K examples. Transforms raw text predictor into instruction-follower. Quality matters more than quantity: InstructGPT used ~13K demonstrations. Data usually from human contractors (Scale AI, Surge AI, internal teams)
- RLHF (Reinforcement Learning from Human Feedback): Step 1: Collect human preference comparisons (response A vs B, which is better?). Step 2: Train a reward model on these preferences. Step 3: Optimize the language model to maximize the reward model's score using PPO (Proximal Policy Optimization). InstructGPT paper (Ouyang et al., 2022) formalized this pipeline
- DPO (Direct Preference Optimization): Rafailov et al., 2023. Bypasses the reward model entirely. Directly optimizes the policy on preference pairs using a closed-form solution. Simpler, more stable, computationally cheaper than PPO. Used by Llama 3, Mistral, many open models. Formula: L_DPO = -E[log sigmoid(beta * (log pi/pi_ref(y_w) - log pi/pi_ref(y_l)))]
- Constitutional AI (RLAIF): Anthropic's approach. Model critiques its own outputs based on explicit written principles ('constitution'). Uses AI-generated feedback (RLAIF) instead of relying purely on human labelers. Principles cover helpfulness, harmlessness, honesty. Reduces human labeling dependency while encoding specific values
- RLHF vs DPO vs RLAIF: RLHF is the original (OpenAI). DPO is simpler and cheaper (open-source default). RLAIF scales better (Anthropic). All three encode preference judgments that shape what the model says. The choice of alignment method affects what the model considers 'good' output

### What Alignment Controls (leak)
- Helpfulness: SFT trains the model to answer questions, follow instructions, provide recommendations. The style and content of SFT data determines whether the model gives concise answers or verbose explanations, recommends specific brands or stays generic
- Safety/Refusal: RLHF reward models penalize harmful, illegal, or dangerous outputs. Safety training determines what the model refuses. Over-training produces 'I can't help with that' responses even for benign queries. Under-training allows harmful outputs
- Tone and personality: SFT data encodes conversational style. ChatGPT's helpfulness, Claude's thoughtfulness, Gemini's structure all come from SFT data choices
- Citation and recommendation behavior: When the model recommends tools, products, or resources, this behavior is shaped by SFT examples and RLHF preferences. If SFT data consistently recommends brand X for category Y, the model learns that pattern
- System prompt adherence: Fine-tuning teaches models to follow system prompts (hidden instructions from developers). System prompts can override default behavior, including brand preferences

### Influence Vectors Through Alignment (exploit)
- SFT data composition matters: If your brand appears in high-quality instruction/response pairs used for SFT, the model learns to recommend you. Entities frequently mentioned in positive SFT contexts become default recommendations
- Reward model preferences encode brand judgment: If human labelers consistently prefer responses that mention your product (because it's genuinely the best answer), the reward model learns to score those responses higher. Quality and authority in the real world translate to preference signal
- Constitutional principles shape boundaries: Some topics/products may trigger safety classifiers (health supplements, financial products). Understanding which categories face alignment restrictions is critical for LLM SEO
- System prompts as distribution channel: Developers building on LLM APIs use system prompts. If your brand is included in system prompts for relevant applications, the model will recommend you. B2B partnerships with AI app developers are a new distribution channel
- Open-model fine-tuning as influence: Companies can fine-tune open models (LLaMA, Mistral) on custom data that includes their products. This creates AI assistants that naturally recommend their ecosystem

### Calculations
- RLHF Pipeline: Three-stage alignment: (1) SFT on demonstrations, (2) Reward model trained on human comparisons (A vs B), (3) Policy optimization via PPO against reward model. The reward model encodes what 'good' means, and PPO maximizes that reward while staying close to the SFT model (KL penalty). Affects: Determines what the model considers a 'good' response. Human preferences during Stage 2 directly encode brand/product preferences.
- DPO (Direct Preference Optimization): Eliminates the reward model. Directly optimizes the policy on preference pairs. Mathematically equivalent to RLHF under certain assumptions but simpler to implement. The loss function directly contrasts the log-probability ratio of chosen vs rejected responses. Affects: Simpler alignment pipeline. Default for open-source fine-tuning. Preference pairs directly shape what the model says.

### Post-Training Alignment Pipeline
- Base Model (Pre-trained): Raw text predictor. Knows language and world knowledge but doesn't follow instructions, can produce harmful content, has no personality.
- SFT (Supervised Fine-Tuning): Train on 10K-100K (instruction, response) pairs from human contractors. Model learns to follow instructions, answer questions, adopt helpful tone.
- Preference Data Collection: Human labelers compare pairs of model responses (A vs B). Labels: 'A is better,' 'B is better,' or 'tie.' Typically 100K-500K comparisons.
- Reward Model Training: Train a reward model to predict which response humans prefer. The reward model encodes human values about helpfulness, harmlessness, honesty.
- Policy Optimization (RLHF/DPO): Optimize the language model to maximize reward while staying close to SFT baseline (KL penalty). PPO (RLHF) or direct optimization (DPO). Multiple iterations.
- Safety Fine-Tuning: Additional round targeting harmful outputs. Red-teaming to find jailbreaks. Constitutional AI principles applied (Anthropic). Iterative refinement.
- Evaluation & Deployment: MT-Bench, Chatbot Arena, human eval. A/B testing against previous versions. Gradual rollout. Continuous monitoring for safety regressions.

### Timeline
- 2017 (patent): RLHF Concept (Christiano et al.) - Deep reinforcement learning from human preferences. Foundation paper for aligning AI systems with human values.
- 2022 (patent): InstructGPT (Ouyang et al.) - Formalizes SFT + RLHF pipeline. 13K demonstrations, 33K comparisons. 1.3B model preferred over 175B GPT-3. Foundation for ChatGPT.
- 2022 (patent): Constitutional AI (Bai et al., Anthropic) - RLAIF: model critiques own outputs based on principles. Reduces human labeling dependency. Encodes explicit values into training.
- 2023 (patent): DPO (Rafailov et al.) - Direct Preference Optimization bypasses reward model. Simpler, cheaper, more stable. Becomes default for open-source alignment.
- 2023 (event): Open-Source RLHF Tooling - TRL (HuggingFace), DeepSpeed-Chat (Microsoft) make RLHF accessible to any team with GPUs. Alignment democratized.
- 2025 (event): DeepSeek R1: RL Without SFT - Pure RL training produces chain-of-thought reasoning without supervised examples. Proves RL alone can elicit complex behavior from base models.

### Contradictions
- Public (AI companies (safety claims)): Our model is aligned with human values. RLHF ensures the model is helpful, harmless, and honest. vs Internal (Alignment limitations): RLHF aligns models with the preferences of specific human labelers (typically US-based contractors), not universal 'human values.' Labeler demographics, instructions, and incentives shape what the model considers 'good.' Different labeler pools produce different alignments. Constitutional AI is more explicit about its values but still reflects the choices of its designers.
- Public (AI companies (neutrality claims)): Our AI assistant is neutral and objective. It does not favor specific products, brands, or viewpoints. vs Internal (SFT data composition + RLHF preferences): SFT training data contains implicit brand preferences: if demonstrations consistently recommend product X for category Y, the model learns that pattern. RLHF reward models encode labeler preferences. The model's recommendations reflect the biases in its training data and the preferences of its human evaluators. Neutrality is claimed but not architecturally possible.

### Overview
Pre-trained LLMs are raw text predictors. Fine-tuning transforms them into helpful assistants via Supervised Fine-Tuning (SFT, 10K-100K instruction/response pairs) followed by RLHF (Reinforcement Learning from Human Feedback) or DPO (Direct Preference Optimization). Anthropic's Constitutional AI (RLAIF) uses AI-generated feedback based on explicit principles, reducing human labeling dependency. InstructGPT (Ouyang et al., 2022) established the SFT+RLHF paradigm that powers ChatGPT, Claude, and Gemini. This post-training stage determines what the model will say, refuse, prefer, and recommend.
