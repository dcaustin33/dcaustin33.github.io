---
title: "A Python Engineer's Introduction to 3D Gaussian Splatting (Part 1)"
date: 2024-06-11
categories: [Computer Vision, 3D Rendering]
tags: [gaussian-splatting, python, COLMAP, neural-rendering, computer-vision]
---

I wanted to understand 3D Gaussian Splatting from the ground up, but I kept running into the same problem: almost every implementation is written in CUDA, and most explanations assume you already know your way around camera matrices and graphics pipelines. So I wrote this tutorial for people like me, Python engineers with ML experience who haven't spent much time in the graphics world.

The goal is to walk through the forward pass of Gaussian Splatting in pure Python. We start with a COLMAP scan (a set of 3D points extracted from multiple images using Structure from Motion) and project those points onto a 2D image plane. It's not a full Gaussian Splat scene, but it gives you the foundation to understand what's actually happening under the hood.

Here's what Part 1 covers:

- **COLMAP and Structure from Motion.** How SfM identifies matching points across images to estimate their 3D positions, similar to how human stereo vision works
- **Extrinsic matrices.** The quaternion and translation vectors from COLMAP tell us where the camera was and which direction it was facing. Together they form a 4x4 matrix that converts points from world coordinates to camera coordinates
- **Intrinsic matrices.** The camera's focal length and principal point give us a 3x4 matrix that projects 3D camera coordinates onto a 2D image plane
- **Putting it together.** Multiply the extrinsic by your 3D points, then the intrinsic by the result, divide by the depth coordinate, and you get pixel locations on the image

The companion Jupyter notebook walks through all of this with working code so you can follow along step by step. Part 2 covers the actual "Gaussian" part of Gaussian Splatting.

Read the full article on Medium: [A Python Engineer's Introduction to 3D Gaussian Splatting (Part 1)](https://medium.com/data-science/a-python-engineers-introduction-to-3d-gaussian-splatting-part-1-e133b0449fc6)
