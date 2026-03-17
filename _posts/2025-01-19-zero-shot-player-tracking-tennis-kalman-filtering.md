---
title: "Zero-Shot Player Tracking in Tennis with Kalman Filtering"
date: 2025-01-19
categories: [Computer Vision, Tracking]
tags: [computer-vision, object-detection, kalman-filter, tennis, GroundingDINO, homography]
---

Automated player tracking in sports typically requires collecting labeled data and training a dedicated detection model. In this project, I bypass that entirely using GroundingDINO's zero-shot object detection — prompting the model with "a tennis player" — combined with a custom Kalman filter to produce smooth, persistent tracking from broadcast footage without a single label.

The dataset comes from publicly available 2012 Olympics footage at Wimbledon (MIT Licensed), focusing on a match between Serena Williams and Victoria Azarenka.

## The Challenge

Zero-shot detection on a tennis court is noisy. GroundingDINO frequently picks up line judges, ball people, and umpires alongside the actual players, and detections can flicker or disappear between frames. Simply filtering to the top-N boxes isn't enough since line judges often score higher confidence than the players themselves.

## The Approach

To solve this, I built a multi-object tracking pipeline using Kalman filters with Mahalanobis distance-based association:

- **Kalman filter state tracking** — Each detected object maintains a state vector (position + velocity) with adaptive process noise, enabling prediction through missed detections
- **Object association** — New detections are matched to tracked objects via Mahalanobis distance with chi-squared testing, with new tracks spawned when no match exceeds 30% probability
- **Player identification** — Rather than using proximity to the baseline (which fails when players move), the two objects with the longest continuous tracking history are selected as players — a simple heuristic that works surprisingly well since GroundingDINO flickers between court personnel but maintains relatively stable player detections

## Bird's-Eye View with Homography

With player positions identified in image space, I use OpenCV's homography estimation to project coordinates onto a standardized overhead court template. Court keypoints (line intersections) are identified and matched to reference coordinates, and the bottom-center of each player's bounding box is transformed to get their court position. This enables analysis of distance traveled, player speed, and court positioning patterns.

## Takeaways

The pipeline demonstrates that zero-shot detection combined with classical tracking can produce useful player tracking without any labeled training data. With further refinement — like distilling a YOLO model from GroundingDINO outputs — this could approach real-time performance for coaching and match analytics applications.

Read the full article on Medium: [Zero-Shot Player Tracking in Tennis with Kalman Filtering](https://medium.com/data-science/zero-shot-player-tracking-in-tennis-with-kalman-filtering-80bba73a4247)
