---
title: "Zero-Shot Player Tracking in Tennis with Kalman Filtering"
date: 2025-01-19
categories: [Computer Vision, Tracking]
tags: [computer-vision, object-detection, kalman-filter, tennis, GroundingDINO, homography]
---

I wanted to see if I could track tennis players from broadcast footage without labeling a single frame. Most sports tracking projects start with collecting labeled data and training a YOLO model, but GroundingDINO lets you skip all that by prompting an object detector with plain text like "a tennis player." I paired it with a Kalman filter to smooth out the noisy detections and tested it on publicly available 2012 Olympics footage from Wimbledon (MIT Licensed), focusing on a Serena Williams vs. Victoria Azarenka match.

The tricky part is that "tennis player" picks up way more than just the players. Line judges, ball kids, and umpires all get detected, and sometimes they score higher confidence than the actual players. On top of that, detections flicker on and off between frames.

Here's how I dealt with it:

- **Kalman filter tracking.** Each detected object gets its own tracker that maintains position and velocity, so even when GroundingDINO drops a detection for a few frames the filter predicts where the object should be
- **Mahalanobis distance for matching.** New detections get associated with existing tracks probabilistically. If nothing matches above 30% probability, a new track gets created
- **Longest track = real player.** I tried picking boxes closest to the baseline but that breaks when players move to the net. What actually worked was just selecting the two tracks with the longest history, since GroundingDINO flickers between court personnel but tracks players more consistently

Once I had player positions in image space, I used OpenCV's homography to project them onto an overhead court template. You label a few court keypoints (line intersections), match them to reference coordinates, and you get a transformation matrix that maps the bottom-center of each bounding box onto a bird's eye view. From there you can analyze distance covered, speed, and court positioning.

The whole pipeline works surprisingly well for how simple it is. A natural next step would be distilling the GroundingDINO outputs into a faster model like YOLO or RT-DETR to get closer to real-time.

Read the full article on Medium: [Zero-Shot Player Tracking in Tennis with Kalman Filtering](https://medium.com/data-science/zero-shot-player-tracking-in-tennis-with-kalman-filtering-80bba73a4247)
