---
title: "The AI Research Deep Dive: An Agentic Podcast Pipeline"
date: 2025-07-15
categories: [Machine Learning, Agents]
tags: [LLM, agents, podcast, TTS, Gemini, agentic-workflows]
---

I read a decent amount of AI/ ML papers and wanted a way to consume on my way to work. I found that tools like NotebookLM that generate podcast-style audio tend to stay surface-level and I wanted something deeper. I wanted something that actually digs into the methodology, validates the results against the claimed contributions, and calls out limitations. So I built an agentic pipeline that takes a PDF and produces a 15-minute technical podcast episode.

The system works in stages where each step builds on the outputs of prior steps. First, the PDF gets compressed (downsampled images, stripped metadata) and fed to an LLM that extracts the paper's key contributions and why the paper matters in the grand scheme of things. That output then feeds into three parallel analyses (method, results, limitations), each grounded in those extracted contributions. All four outputs get passed to a final LLM call that synthesizes everything into a single coherent podcast script, which then gets converted to audio via Kokoro or ElevenLabs.

The agentic part is what makes it work. The results analysis knows what the paper claims (from the contributions step) and can specifically evaluate whether the experiments back those claims up. The methodology section knows the contributions and can explain how each one was achieved. I found that without separating these sections out we tended to get generic surface level outputs that restated the abstract of the paper.

The hardest problems were all about the text-to-audio boundary. Math symbols, markdown formatting, and figure references all sound terrible when read aloud. The prompts enforce plain English output (spell out numbers, expand acronyms, no LaTeX), and there's post-processing to strip any remaining markdown artifacts. Figures and tables get described conversationally rather than referenced by number.

The whole thing runs on Gemini 2.5 Flash and GPT-4o-mini with PDF vision, so the models see the actual paper including figures and tables rather than just extracted text. Async execution via `asyncio.gather()` runs the parallel analyses concurrently, cutting total generation time significantly.

Listen on Spotify: [The AI Research Deep Dive](https://open.spotify.com/show/3aJSYxqCU6J0y6YnIhAIcA)

Check out the code: [podcaist](https://github.com/dcaustin33/podcaist)
