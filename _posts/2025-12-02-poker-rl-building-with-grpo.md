---
title: "Poker RL: Building with GRPO"
date: 2025-12-02
categories: [Machine Learning, Reinforcement Learning]
tags: [reinforcement-learning, GRPO, poker, LLM, RLVR]
---

Can reinforcement learning with verifiable rewards (RLVR) teach a language model to play poker? In this project I explored exactly that — combining supervised fine-tuning on high-quality poker datasets (PokerBench and Pluribus) with Group Relative Policy Optimization (GRPO) to train small models like Gemma-3-1B and Qwen-3-Embedding-0.6B into competitive poker agents.

The approach treats poker as a multi-turn RL problem: the model takes in a textual representation of the game state and outputs action logits (fold, call, raise, all-in) through auxiliary heads. After SFT on labeled hand histories, GRPO refines play by sampling multiple rollouts of the same hand and rewarding actions that lead to above-average outcomes — no human feedback required.

Key findings from the experiments:

- **Embedding models outperformed LLMs** — Qwen-Embedding-0.6B beat Gemma-3-1B by $1.78/game, challenging my initial assumption that LLM pretraining would provide a strategic edge
- **Population-based training improved stability** — keeping a pool of former model versions as opponents (RLv2) outperformed pure self-play (RLv1) by $2/game
- **Scaling compute helped** — every later checkpoint beat earlier ones, with the final model beating the epoch-10 checkpoint by ~$1/game
- **Entropy collapse remains the key challenge** — especially at larger model sizes (4B), aggressive entropy regularization is needed but comes with tradeoffs
- **Evolutionary strategies fell short** — despite maintaining healthy entropy, they failed to consistently improve over the SFT baseline

All experiments ran for under $50 on a single GH200 GPU. The trained agents developed an ultra tight-aggressive playstyle, learning behaviors like blind stealing and aggressive all-ins with strong hands.

Read the full article on Medium: [Poker RL: Building with GRPO](https://medium.com/gitconnected/wpoker-rl-building-with-grpo-b0dda1aebdc3)

Check out the code:
- [Training repo](https://github.com/dcaustin33/poker_rl)
- [Datasets repo](https://github.com/dcaustin33/poker_datasets)
