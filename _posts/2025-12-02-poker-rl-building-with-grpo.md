---
title: "Poker RL: Building with GRPO"
date: 2025-12-02
categories: [Machine Learning, Reinforcement Learning]
tags: [reinforcement-learning, GRPO, poker, LLM, RLVR]
---

I wanted to see if I could take a small language model and teach it to play poker using reinforcement learning with verifiable rewards (RLVR). The idea is pretty simple: poker gives you a clear reward signal (you either win or lose chips), so why not let a model play thousands of hands and learn from the outcomes?

I started by fine-tuning on high-quality poker datasets (PokerBench and Pluribus), then used Group Relative Policy Optimization (GRPO) to improve play through self-play simulations. The model takes in a text description of the game state and outputs actions (fold, call, raise, all-in) through auxiliary heads rather than generating text. GRPO works by playing the same hand multiple times and reinforcing the actions that led to better-than-average results, no human feedback needed.

Here's what I found after a month of experimentation:

- **An embedding model outperformed a pure LLM.** Qwen-Embedding-0.6B beat Gemma-3-1B by 1.78 BB/game, despite having fewer parameters
- **Playing against older versions of yourself helps.** Keeping a pool of former model checkpoints as opponents (RLv2) beat pure self-play (RLv1) by 2 BB/game
- **More compute keeps helping.** Every later checkpoint beat earlier ones, with the final model winning ~1 BB/game over the epoch-10 checkpoint
- **Entropy collapse is the biggest headache.** Especially at 4B parameters, the model gets overconfident in its actions and stops exploring
- **Evolutionary strategies didn't work here.** They kept entropy healthy but couldn't consistently improve over the supervised baseline

All experiments cost under $50 on a single GH200 GPU. The trained agents ended up playing an ultra tight-aggressive style, learning to steal blinds and shove all-in with strong pocket pairs.

Read the full article on Medium: [Poker RL: Building with GRPO](https://medium.com/gitconnected/wpoker-rl-building-with-grpo-b0dda1aebdc3)

Check out the code:
- [Training repo](https://github.com/dcaustin33/poker_rl)
- [Datasets repo](https://github.com/dcaustin33/poker_datasets)
