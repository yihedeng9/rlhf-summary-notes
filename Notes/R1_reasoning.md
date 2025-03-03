# R1 Reasoning
[DeepSeek-R1 tech report](https://arxiv.org/abs/2501.12948) explores how reinforcement learning (RL) can spark advanced chain-of-thought reasoning in language models. Notably, DeepSeek-R1 introduced the concept of a sudden “**aha moment**” during RL training with verifiable rewards, in which the model rapidly develops more sophisticated reasoning and self-reflection. This discovery, among with many others, has inspired a wave of open-source reproduction and extension projects. 

Below, I summarize some key notes and insights from these follow-up efforts. For a deeper dive into the technical underpinnings of DeepSeek-R1 itself and the below blog/papers, please refer to the original papers for a complete read.

## Open-Source Reproduction Projects
[**Open-R1**](https://github.com/huggingface/open-r1/)
- HuggingFace's open reproduction of the DeepSeek-R1 project, aiming to replicate the entire pipeline described in the original DeepSeek-R1 tech report. 
-   Including model training (SFT and GRPO) and evaluation on tasks like AIME 2024, MATH-500, and code generation benchmarks. 

[**Tiny-Zero**](https://github.com/Jiayi-Pan/TinyZero)
- Reproduce DeepSeek R1-Zero in the countdown and multiplication tasks, using `Qwen2.5-3B-Instruct` that developed self-verification through RL.
- With PPO, GRPO and PRIME, the team discovered that long CoT all emerge and seem to all work well.



## Blogs
[**DeepScaleR: Surpassing O1-Preview with a 1.5B Model by Scaling RL**](https://pretty-radio-b75.notion.site/DeepScaleR-Surpassing-O1-Preview-with-a-1-5B-Model-by-Scaling-RL-19681902c1468005bed8ca303013a4e2)
- Github repo: https://github.com/agentica-project/deepscaler 
- Authors present [**DeepScaleR-1.5B-Preview**](https://huggingface.co/agentica-org/DeepScaleR-1.5B-Preview), a 1.5B-parameter language model finetuned via RL on math reasoning tasks. It surpasses O1-Preview on AIME2024 and performs strongly across multiple competition-level math benchmarks.
- The work builds on DeepSeek-R1-Distill-Qwen-1.5B.
- **Iterative Context Lengthening** (“Think Shorter, Then Longer”)
    - A critical challenge is the long outputs for math reasoning, which slow training and can lead to repetitive, unhelpful content. The team considers a staged approach with GRPO:
        - Begin with an 8K context window to keep outputs shorter and focus on correct reasoning.
        - Increase to 16K once the model plateaus and starts hitting the 8K limit.
        - Finally, increase to 24K to fully exploit longer chain-of-thought once the model has learned to reason more concisely.

[**Open-Reasoner-Zero**](https://yasminezhang.notion.site/Open-Reasoner-Zero-19e12cf72d418007b9cdebf44b0e7903)
- Github repo: https://github.com/Open-Reasoner-Zero/Open-Reasoner-Zero, [PDF](https://github.com/Open-Reasoner-Zero/Open-Reasoner-Zero/blob/main/ORZ_paper.pdf)
- The authors collect and clean a dataset of 57k reasoning and math problems, combining open-source math and logic tasks and some synthetic samples.
    - They show that scaling the dataset (e.g., going from ~7k to ~57k samples) significantly improves both response correctness and response length (i.e., deeper reasoning steps).
- As training proceeds, models’ response lengths grow steadily, mirroring prior observations from systems like DeepSeek-R1-Zero.
- **Emergent “Aha Moments"**
    - "Step moment": the report highlights “step-function” jumps, where the model suddenly transitions to substantially better performance and longer, more thorough solutions.
    - The authors also observe increased average correct reflection length once the model passes certain training thresholds (Figure 7 of the [PDF](https://github.com/Open-Reasoner-Zero/Open-Reasoner-Zero/blob/main/ORZ_paper.pdf)).

[**Online-DPO-R1: Unlocking Effective Reasoning Without the PPO Overhead**](https://efficient-unicorn-451.notion.site/Online-DPO-R1-Unlocking-Effective-Reasoning-Without-the-PPO-Overhead-1908b9a70e7b80c3bc83f4cf04b2f175)
- Github repo: https://github.com/RLHFlow/Online-DPO-R1
- Rule-based approaches like iterative DPO and RAFT can substantially enhance a capable base model’s performance on challenging math benchmarks, while remaining simpler to implement than full PPO training.
    - While iterative DPO is simpler to implement and yields strong gains, PPO-based models still reach the highest overall performance. Nonetheless, the gap between DPO and PPO narrows substantially if the model is **warm-started with SFT**.
- **Importance of a strong base model**: attempts to apply iterative DPO on `Llama-3-8B-Instruct` did not yield significant improvements. The authors suggest that Qwen2.5’s stronger math pre-training allows rule-based RL to succeed.
- **“Aha Moments” and self-reflection**
    - The authors did not observe a clear “aha moment” with small-scale open-source models. Instead, they note that Qwen2.5-Math-7B-Base already uses reflective reasoning patterns, and neither iterative DPO nor PPO specifically increase their frequency.

[**There May Not be Aha Moment in R1-Zero-like Training — A Pilot Study**](https://oatllm.notion.site/oat-zero)
- Github repo: https://github.com/sail-sg/oat-zero
- The study challenges the common observation that this "**Aha Moment**" occurs during training, discovering that
    - Aha moment at epoch 0: Self-reflection patterns were observed in base models from the start, without requiring any post-training. This suggests the "Aha moment" is present right from the beginning.
    - **Superficial Self-Reflection** (SSR): While some models show self-reflection behavior, many of these are not effective and do not lead to correct answers. 
    - Response length and RL dynamics: The increasing response length often associated with the "aha moment" is not linked to emergent self-reflection but results from RL optimizing rule-based reward functions. The paper demonstrates that response length changes are a consequence of RL dynamics and not necessarily related to improved reasoning.

## Relevant literatures
[**Demystifying Long Chain-of-Thought Reasoning in LLMs**](https://arxiv.org/abs/2502.03373)
- A detailed analysis of longCoT reasoning in LLMs. These “long CoTs” often include branching, backtracking, error-checking, and other advanced features reminiscent of deeper analytical thinking. 
- SFT with Long CoTs
    - Fine-tuning a base model on long CoT data consistently outperforms short CoT training and continues improving with more data.
- Reward shaping for stable length growth
    - Simply relying on a verifiable reward can lead to unstable or explosive **CoT length growth**, causing outputs to exceed the context window and degrade performance.
    - The authors introduce a cosine reward function (varies by answer correctness and sequence length) to help stabilize length scaling. 
    - In addition, applying a repetition penalty mitigates **reward hacking** where models add repeated content just to accumulate more reward. 
- Importance of **verifiable signals**
    - RL becomes more stable and effective when the model’s final answers can be verified using ground-truth signals.
- RL from base models vs. long-CoT–SFT models
    - Training RL directly from smaller base models sometimes fails to spark the “aha” behaviors.
    - Initializing RL with a model already tuned for long CoT consistently yields more robust exploration of longer reasoning paths. 
- Latent Abilities and Data Origins
    - The paper also suggests that capabilities like error-correction **may already exist as latent skills in base models** (due to large-scale pretraining on forum discussions and step-by-step solutions). 
    - RL and SFT can bring these “buried” skills to the surface with proper rewards, penalties, and prompts.

[**LIMO: Less is More for Reasoning**](https://arxiv.org/abs/2502.03387)
- The authors challenge the assumption that complex mathematical reasoning requires massive amounts of fine-tuning data. Instead, they show that using fewer than 1k carefully curated training examples can dramatically enhance a model’s ability to solve advanced math problems.
- *The LIMO Hypothesis*: In foundation models that already have extensive domain knowledge “baked in” from pre-training, sophisticated reasoning capabilities can emerge with only a small number of carefully designed demonstrations.
- Related: [**LIMR: Less is More for RL Scaling**](https://arxiv.org/abs/2502.11886)

[**Logic-RL: Unleashing LLM Reasoning with Rule-Based Reinforcement Learning**](https://arxiv.org/abs/2502.14768)
- Authors use Knights and Knaves (K&K) puzzles as controlled data that allows precise measurement of logical reasoning performance.
    - They generate Knights and Knaves puzzles of varying complexity (2–8 inhabitants), ensuring each puzzle has a unique, verifiable solution.
    - This procedural approach eliminates ambiguity and makes reward-checking straightforward.
- Modified REINFORCE++ algorithm
    - Instead of PPO or GRPO, the authors use a modified REINFORCE approach.
    - Key tweaks include a KL-divergence penalty term to avoid drifting too far from the base model’s distribution and a specialized KL estimator.
- Emergent reasoning behaviors
    - As training progresses, the model begins to show verification, multi-path exploration, and self-reflection in the <think> section.
    - Response length tends to grow over training, but **length alone is not a reliable indicator** of improved reasoning accuracy.
- Generalization beyond training data
    - Despite training on only 5K synthetic logic puzzles, the model demonstrates a transfer effect to tougher math benchmarks like AIME.
    - The authors note it outperforms a simple SFT approach, which tends to memorize specific formats rather than develop robust reasoning.

---

More related works:

[**s1: Simple test-time scaling**](https://arxiv.org/abs/2501.19393)

[**Scaling Test-Time Compute Without Verification or RL is Suboptimal**](https://arxiv.org/abs/2502.12118)

[**Can 1B LLM Surpass 405B LLM? Rethinking Compute-Optimal Test-Time Scaling**](https://arxiv.org/abs/2502.06703)

[**Self-Training Elicits Concise Reasoning in Large Language Models**](https://arxiv.org/abs/2502.20122)

[**Scaling up Test-Time Compute with Latent Reasoning: A Recurrent Depth Approach**](https://arxiv.org/abs/2502.05171)

[**Thinking Preference Optimization**](https://arxiv.org/abs/2502.13173)

[**LightThinker: Thinking Step-by-Step Compression**](https://arxiv.org/abs/2502.15589)

[**Self-rewarding correction for mathematical reasoning**](https://arxiv.org/abs/2502.19613)

## Surveys
[**From System 1 to System 2: A Survey of Reasoning Large Language Models**](https://arxiv.org/abs/2502.17419)