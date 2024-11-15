# RLHF Summary Notes
A brief and partial summary of RLHF algorithms.
This repository summarizes a list of papers and useful blogs for RLHF that are covered in my reading group presentation of a brief summary for RLHF algorithms. Please find the slides [here](Reading_Group_RLHF.pdf). 

## Why RLHF?
LLMs pre-trained on large text corpus express unintended behaviors such as hallucinations, bias/toxicity, or failure to follow instructions.
- Misaligned: language modeling objective (next token prediction) is different from the objective of human values (helpful, honest, harmless).

RLHF is proposed to align a model trained on general corpus to complex human values.
- Use human feedback for generated text as a measure of performance and use that feedback as a loss to optimize the model.
- Use methods from RL to directly optimize a language model with human feedback.

## Learning from (Human/AI) Preference Feedback

1. Preference Reward Modeling 
    - Requires building a reward model based on user preferences, optimized with RL, typically using the PPO (Proximal Policy Optimization) algorithm.
    - Computationally expensive and sensitive to hyper-parameter selection.

2. Direct Preference Optimization
    - Views preference optimization as offline RL, with implicit reward model.
    - Starting with DPO (Direct Preference Optimization), the evolution of variations of DPO aims in adjusting its loss function, with ongoing fixes that make it more RL-like and overcome existing weaknesses.

## Online RL
- [Training language models to follow instructions with human feedback.](https://arxiv.org/abs/2203.02155)
- (Summary) [RLHF Workflow: From Reward Modeling to Online RLHF.](https://arxiv.org/pdf/2405.07863v1)
- (Summary) [Secrets of RLHF in Large Language Models Part I: PPO.](https://arxiv.org/abs/2307.04964)
- (Algorithm) PPO: [Proximal Policy Optimization Algorithms.](https://arxiv.org/abs/1707.06347)
- (Algorithm) RLOO: [Back to Basics: Revisiting REINFORCE Style Optimization for Learning from Human Feedback in LLMs.](https://arxiv.org/abs/2402.14740)
- (Algorithm) GRPO: [DeepSeekMath: Pushing the Limits of Mathematical Reasoning in Open Language Models.](https://arxiv.org/pdf/2402.03300)

## Offline RL
- DPO: [Direct Preference Optimization: Your Language Model is Secretly a Reward Model.](https://arxiv.org/abs/2305.18290) 
- RPO: [Iterative Reasoning Preference Optimization.](https://arxiv.org/abs/2404.19733)
- KTO: Model Alignment as Prospect Theoretic Optimization.
- Direct Nash Optimization: Teaching Language Models to Self-Improve with General Preferences.
- Step-DPO: Step-wise Preference Optimization for Long-chain Reasoning of LLMs
- Self-Rewarding Language Models
- Self-Play Fine-Tuning Converts Weak Language Models to Strong Language Models
- ReST-MCTS∗: LLM Self-Training via Process Reward Guided Tree Search.
- Flow-DPO: Improving LLM Mathematical Reasoning through Online Multi-Agent Learning
- Monte Carlo Tree Search Boosts Reasoning via Iterative Preference Learning. 
- Building Math Agents with Multi-Turn Iterative Preference Learning. 
- Disentangling Length from Quality in Direct Preference Optimization. 
- SimPO: Simple Preference Optimization with a Reference-Free Reward.
- Preference Ranking Optimization for Human Alignment.
- LiPO: Listwise Preference Optimization through Learning-to-Rank.
- Self-Consistency Preference Optimization.
- Aligning modalities in vision large language models via preference fine-tuning.
- Enhancing Large Vision Language Models with Self-Training on Image Comprehension.


## Useful Blogs
- [Policy Gradient Algorithms](https://lilianweng.github.io/posts/2018-04-08-policy-gradient/) 
- [Going Deeper Into Reinforcement Learning: Fundamentals of Policy Gradients](https://danieltakeshi.github.io/2017/03/28/going-deeper-into-reinforcement-learning-fundamentals-of-policy-gradients/)
- [Illustrating Reinforcement Learning from Human Feedback (RLHF)](https://huggingface.co/blog/rlhf)
- [Proximal Policy Optimization (PPO)](https://huggingface.co/blog/deep-rl-ppo)
- [关于LLM+RL(HF)的片面脉络梳理](https://zhuanlan.zhihu.com/p/1686790674?utm_psn=1833144248435879936) 
- [Advanced Tricks for Training Large Language Models with Proximal Policy Optimization](https://www.notion.so/eb7b2d1891f44b3a84e7396d19d39e6f?v=01bcb084210149488d730064cbabc99f) 