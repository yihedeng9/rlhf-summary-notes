# Reward Hacking, Shortcut Learning, and Spurious Correlation

**Authors:**  [**Yihe Deng**](https://www.notion.so/Yihe-Deng-167ab2d2c1fb80b3a76dfb120f716c84?pvs=21) ([twitter](https://x.com/Yihe__Deng)),  [**Yu Yang**](https://sites.google.com/g.ucla.edu/yuyang/home) ([twitter](https://x.com/YuYang_i))

**Date:** 12/23/2024  

Thanks to [Fan Yin](https://fanyin3639.github.io/) for providing invaluable feedback on an earlier draft of this blog.

> *Spurious correlation or shortcut learning ([Geirhos et al. 2020](https://arxiv.org/abs/2004.07780)) in classification tasks is closely related to reward hacking.*  
> — [**Reward Hacking in Reinforcement Learning**](https://lilianweng.github.io/posts/2024-11-28-reward-hacking/#rl-algorithm-improvement)

This note focuses on the connection between reward hacking and spurious correlation. **Please see the [blog](https://yihe-deng.notion.site/) for detailed discussion and a comprehensive references list.**

## 1. Introduction

### Reward Hacking (Classic RL Context)
- **Definition**: Occurs when RL agents exploit flaws in the reward function to produce unintended behaviors (e.g., looping for infinite points, crashing the environment).
- **Key Point**: Misalignment between the reward function and the true goal leads to perverse strategies.

### Data-Induced Reward Hacking
- In **preference-based** training (e.g., RLHF, LLM alignment), reward hacking can take the form of **spurious correlations**: the model learns superficial patterns (e.g., lengthier text = better) rather than genuine quality.
- Reflects **Goodhart’s Law**: overoptimizing a proxy (human preference data) invalidates that proxy’s utility.

## 2. Spurious Correlation

A **spurious correlation** is a superficial feature strongly associated with the target label but not causally related. Classic examples include:
- **Vision**: Background colors, water vs. land, or demographic attributes that overshadow the core features.
- **Language**: Negation words, certain templates, or keywords (e.g., “safe,” “ethical”) correlated with higher or lower labels.

Spurious correlations often lead to **shortcut learning**, degrading model robustness, especially for minority or out-of-distribution examples.

## 3. How Preference Data Can Contain Spurious Correlations

### Length as a Proxy for Quality
- **Longer Responses** often earn higher human ratings for thoroughness or politeness.  
- **DPO (Direct Preference Optimization)** can inadvertently overfit to “longer = better,” causing the model to produce excessively long or rambling outputs.

### “Keyword Hacking” in Safety and Alignment
- **Refusal Tokens** (e.g., “I apologize,” “I cannot”) might be overused if preference data consistently labels these as safer or more responsible responses.  
- Shallow alignment becomes easy to bypass and does not address deeper generative behavior.

### Other Potential Correlations
- **Formatting Bias**: Bullet points, special tokens (emojis, exclamation marks), or bold text might inflate user ratings.  
- **Confidence Tone**: Overly confident or enthusiastic language can be misconstrued as correctness or helpfulness.  
- **Positivity Bias**: Cheerful or agreeable responses may be preferred, even when a neutral or critical stance is more accurate.

These spurious features mirror the phenomenon seen in image or text classification, where superficial cues overshadow the true underlying quality or correctness.


## 4. Further Questions

### Does Negative Data Accelerate Shortcut Learning?
- Including clearly dispreferred samples (shorter, impolite, etc.) may reinforce superficial features that separate negative from positive examples.
- **DPO vs. SFT** comparisons reveal t

### Will Iterative Self-Training Amplify Spurious Correlations?
- Multi-round self-improvement cycles can **amplify** existing biases if the model keeps learning from its own biased outputs.
- Without careful monitoring or correction, spurious signals may escalate in each iteration.


## 5. Potential Mitigations

### Algorithmic Approaches
- [**R-DPO**](https://arxiv.org/abs/2403.19159): Adjusts per-example learning rates to balance out length or other spurious signals.  
- [**SimPO**](https://arxiv.org/pdf/2405.14734): Uses regularization to discourage overly long or superficial responses.  
- [**Reward Model Ensembles**](https://arxiv.org/abs/2312.09244): Combines multiple RMs for a more conservative reward signal, though shared biases can still persist.  
- [**ODIN**](https://arxiv.org/abs/2402.07319): Separates reward signals into length-correlated and length-independent heads to discourage length-based exploitation.

### Data-Centric Approaches
- [**RRM**](https://arxiv.org/abs/2409.13156) (Robust Reward Model Training): Augments or balances chosen vs. rejected responses to disrupt spurious correlations.  
- **Group-Based Methods** (e.g., GroupDRO, PDE, JTT): Identify spurious attributes (like backgrounds in vision tasks or style tokens in text) and balance them during training.  
- [**PDE**](https://arxiv.org/abs/2306.04949) (Progressive Data Expansion): Start with balanced subsets before adding potentially imbalanced data, preserving early robustness.

### Theoretical Insights
- Spurious features often dominate because they are **easier to learn** or highly correlated with the label.  
- Distributionally robust strategies and balanced sampling can mitigate these shortcuts by limiting reliance on the superficial signals.


## 6. Conclusion

Reward hacking and spurious correlation are deeply intertwined challenges, underscoring the gap between *true objectives* (e.g., factual correctness, genuine helpfulness) and *over-optimized proxies* (length, specific tokens, or superficial style). While recent advancements (e.g., R-DPO, ODIN, RRM) tackle these shortcuts algorithmically and through data manipulation, fully robust alignment requires:
1. **Better understanding** of which artifacts are truly spurious.
2. **Iterative monitoring** of model outputs and self-training loops.
3. **Data balancing and annotation** strategies that reduce reliance on superficial cues.

> **When Good Writing Hides Bad Content**  
> High-quality formatting and engaging style can mask inaccuracies or shallow reasoning. The real challenge is defining (and enforcing) robust criteria for “good” responses across diverse user needs without encouraging misleading superficial correlations.


## Selected References

**Spurious Correlation** 

Alain et al. [Variance Reduction in SGD by Distributed Importance Sampling](https://arxiv.org/abs/1511.06481). 2015.

Ding et al. [Seeing is not Believing: Robust Reinforcement Learning against Spurious Correlation.](https://arxiv.org/abs/2307.07907) NeurIPS, 2023.

Li et al. [A Whac-A-Mole Dilemma: Shortcuts Come in Multiples Where Mitigating One Amplifies Others.](https://arxiv.org/abs/2212.04825) CVPR, 2023.

McCoy et al. [Right for the Wrong Reasons: Diagnosing Syntactic Heuristics in Natural Language Inference.](https://arxiv.org/abs/1902.01007) ACL, 2019.

**How Preference Data Can Contain Spurious Signals**

[Disentangling Length from Quality in Direct Preference Optimization.](https://arxiv.org/abs/2403.19159) 2024.

[SimPO: Simple Preference Optimization with a Reference-Free Reward.](https://arxiv.org/pdf/2405.14734) 2024.

Qi et al. [Safety Alignment Should Be Made More Than Just a Few Tokens Deep.](https://arxiv.org/abs/2406.05946) 2024.

Zhang et al. [From Lists to Emojis: How Format Bias Affects Model Alignment.](https://arxiv.org/abs/2409.11704) 2024.

Park et al. [OffsetBias: Leveraging Debiased Data for Tuning Evaluators.](https://arxiv.org/abs/2407.06551) 2024

**Further Questions**

Ren et al. [Bias Amplification in Language Model Evolution: An Iterated Learning Perspective.](https://arxiv.org/pdf/2404.04286) 2024.

**Potential Mitigations**

Eisenstein et al. [Helping or Herding? Reward Model Ensembles Mitigate But Do Not Eliminate Reward Hacking.](https://arxiv.org/abs/2312.09244) 2023.

Gao et al. [Scaling Laws for Reward Model Overoptimization.](https://arxiv.org/abs/2210.10760) ICML, 2023.

Chen et al. [ODIN: Disentangled Reward Mitigates Hacking in RLHF.](https://arxiv.org/abs/2402.07319) 2024

Wang et al. [Arithmetic Control of LLMs for Diverse User Preferences: Directional Preference Alignment with Multi-Objective Rewards.](https://arxiv.org/abs/2402.18571) 2024.

Liu et al. [RRM: Robust Reward Model Training Mitigates Reward Hacking.](https://arxiv.org/abs/2409.13156) 2024.

Ramé et al. [WARM: On the Benefits of Weight Averaged Reward Models](https://arxiv.org/abs/2401.12187). ICML, 2024.

**Previous approaches to spurious correlations**

Nam et al. [Learning From Failure: De-biasing Classifier from Biased Classifier.](https://arxiv.org/abs/2007.02561) NeurIPS, 2020.

Liu et al. [Just Train Twice: Improving Group Robustness without Training Group Information.](http://arxiv.org/abs/2107.09044) ICML, 2021.

Creager et al. [Environment Inference for Invariant Learning.](https://proceedings.mlr.press/v139/creager21a/creager21a.pdf) ICML, 2021.

Yang et al. [Identifying Spurious Biases Early in Training through the Lens of Simplicity Bias.](https://arxiv.org/abs/2305.18761) AISTATS, 2024.

Sagawa et al. [Distributionally Robust Neural Networks.](https://arxiv.org/abs/1911.08731) ICLR, 2019.

Kirichenko et al. [Last Layer Re-training is Sufficient for Robustness to Spurious Correlations.](https://arxiv.org/abs/2204.02937) ICLR, 2023.

Deng et al. [Robust Learning with Progressive Data Expansion Against Spurious Correlation.](https://arxiv.org/abs/2306.04949) NeurIPS, 2023.

Sagawa et al. [An Investigation of Why Overparameterization Exacerbates Spurious Correlations.](https://arxiv.org/abs/2005.04345) ICML, 2020.

Lin et al. [Spurious Feature Diversification Improves Out-of-Distribution Generalization.](https://arxiv.org/abs/2309.17230) ICLR, 2024. 