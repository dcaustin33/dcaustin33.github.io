---
title: "Democratizing Basketball Analytics with Computer Vision"
date: 2024-02-15
categories: [Computer Vision, Sports Analytics]
tags: [computer-vision, basketball, VideoMAE, shot-detection, sports, deep-learning]
---

Analytics have completely changed basketball but the tooling is expensive. Companies like Second Spectrum give NBA teams detailed breakdowns of shot quality, defensive coverages, and more, but most high school and college programs can't afford that. I wanted to see how far I could get with open-source computer vision tools to close the gap.

I manually labeled about 8000 shots across 60 NCAA games on YouTube, recording the release frame, shot type, make/miss, and court coordinates. After the first 20 games I used a semi-supervised approach, only reviewing clips where my model flagged a shot probability above 5%, which cut out most of the dead time.

I fine-tuned VideoMAE on 16-frame clips to handle four tasks: detecting whether a shot occurred, identifying the exact frame, localizing where on the court it was taken, and estimating shot quality. Sharing a single backbone with two classifier heads for detection and frame prediction actually outperformed separate models, likely acting as regularization with limited data. Shot detection hit a ROC-AUC of .96 and frame prediction landed within a tenth of a second. Shot localization over 7 court zones reached 78% accuracy after switching from regression to classification, which fixed the model's inability to distinguish mid-range from three-pointers. Shot quality was the weakest task at 60% accuracy with poor calibration.

The vision is simple: any coach submits game footage and gets back shot tracking and efficiency metrics. SwingVision is already doing this for tennis with just an iPhone. There's no reason basketball can't get there too.

Read the full article on Medium: [Computer Vision Enabled Basketball Analytics](https://medium.com/towards-artificial-intelligence/computer-vision-enabled-basketball-analytics-c00c68217e35)

