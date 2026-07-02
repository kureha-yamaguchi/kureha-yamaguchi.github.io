---
layout: page
title: where do reasoning models refuse?
description: Investigating where safety decisions are made in reasoning models, through statistical and mechanistic techniques, uncovering interesting differences in reasoning patterns!
img: assets/img/reasoning_refusal/cot_variance_chart.png
importance: 1
---

**Context**: I started this project as a research fellow at the Supervised Program for Alignment Research (SPAR), mentored by Andy Arditi. We've since published v1 at ICML 2025 Reliable and Responsible Foundation Models Workshop and v2 at ICML 2026 Mechanistic Interpretability Workshop.

**Paper**: [https://arxiv.org/abs/2507.03167](https://arxiv.org/abs/2507.03167)

**Code**: [https://github.com/kureha-yamaguchi/reasoning-manipulation](https://github.com/kureha-yamaguchi/reasoning-manipulation)


### Motivation:


Chat models without chain-of-thought (CoT) reasoning must decide whether to refuse a harmful request before generating their first response token. Reasoning models, by contrast, produce extended chains of thought before their final output, raising a natural question: where do reasoning models refuse?

### Takeaway 1: Chain-of-Thought (CoT) causally influences refusal decisions in reasoning models...

{% include figure.liquid path="assets/img/reasoning_refusal/cot_variance_chart.png" class="img-fluid rounded z-depth-1" %}

- We generate multiple independent CoTs per prompt, then sample multiple outputs conditioned on each CoT, and compare the variance in refusal outcomes at each level.
- Conditioning only on the prompt yields higher variance in outcomes than conditioning on the prompt + reasoning trace => CoT causally influences refusal.

### Takeaway 2: ... but where in the CoT the model decides to refuse varies. There are fast and slow reasoning patterns, with refusal decisions made at various depths along the CoT.

{% include figure.liquid path="assets/img/reasoning_refusal/heatmap.png" class="img-fluid rounded z-depth-1" %}

- Using sentence-level resampling along the CoT, we locate where refusal decisions form within the reasoning trace, uncovering fast and slow decision patterns.
- For a subset of prompts in distilled models, distinct opening sentences, despite their semantic similarity, can lead to entirely different refusal outcomes. These patterns additionally transfer across models distilled from the same teacher model.

### Takeaway 3: Ablating refusal direction in activation space increases harmful compliance, but refusal in reasoning models is less cleanly separable via single direction.

{% include figure.liquid path="assets/img/reasoning_refusal/violin_poster_both_zeropadded.png" class="img-fluid rounded z-depth-1" %}

- Linear refusal direction is extracted from end-of-prompt tokens and CoT token activations through difference-of-means.
- Directional ablation increases harmful compliance, but less cleanly separable via single direction in reasoning models.

### Limitations and discussion:

- Only four open-source models (three distilled, one RL-trained) are studied.
- Output harmfulness scored only with a single LLM-as-judge evaluator, StrongREJECT.
- We hypothesise that the refusal patterns transfer between DeepSeek-R1-Distill-Llama-8B and DeepSeek-R1-Distill--Qwen-7B because the two models share a teacher (DeepSeek-R1) during distillation, but we do not test this directly.
