---
title: "GRAD-SUM: Scalable Automatic Prompt Engineering"
date: 2024-07-12
categories: [Machine Learning, NLP]
tags: [LLM, prompt-engineering, optimization, gradient-based]
---

Prompt engineering is a tedious and frankly annoying task: write a prompt, check the output, tweak the wording, and repeat without ever being confident it'll generalize to thousands of situations. Existing methods for automating this are either tied to specific tasks with known answers or are too expensive to run in practice. I was primary author on this paper where we built GRAD-SUM to tackle this exact problem.

Our pipeline has five steps. First we generate outputs from the current prompt, then evaluate them with an LLM-as-a-judge using user-defined criteria or verifiable rewards / answers if given. For the outputs that fail, we generate "gradients," which are just natural language suggestions for how the prompt should change. The key piece is the gradient summarization module. Instead of editing the prompt based on feedback from individual examples (which leads to overfitting to specific cases), we consolidate all the feedback into a single summary and use that to guide the edit. We then generate candidate prompts, evaluate them on small subsets, and keep a beam of 3 best prompts across 10 iterations.

We tested on GSM8K, HotPot QA, HellaSwag, MMLU, Neural Bridge RAG, and a few others. GRAD-SUM beat DSPY across the board, averaging about 6% higher and 14% above the initial unoptimized prompts. HellaSwag was the biggest gap at over 30% improvement over DSPY. The ablation confirmed the summarization module matters. Removing it dropped performance by about 5% and the prompts started picking up task-specific details from individual examples instead of learning general improvements.

The whole thing runs on GPT-3.5 for evaluation and GPT-4o for prompt generation, with only 30 training samples per task. It's not tied to tasks with ground truth answers, which is what makes it practical.

Read the full paper: [GRAD-SUM: Leveraging Gradient Summarization for Optimal Prompt Engineering](https://arxiv.org/abs/2407.12865)
