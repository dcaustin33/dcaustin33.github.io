---
title: "A Python Engineer's Introduction to 3D Gaussian Splatting (Part 3)"
date: 2024-07-18
categories: [Computer Vision, 3D Rendering]
tags: [gaussian-splatting, python, CUDA, neural-rendering, computer-vision]
---

This is the final part of the series where we actually render an image. [Part 1]({% post_url 2024-06-11-python-engineers-intro-to-3d-gaussian-splatting %}) covered projecting 3D points to 2D, [Part 2]({% post_url 2024-06-12-python-engineers-intro-to-3d-gaussian-splatting-part-2 %}) covered the Gaussian math and covariance projection. Now we take all of that and put pixels on screen.

The rendering logic is honestly the simplest part of the whole pipeline. For each pixel, we iterate through splats in depth order and accumulate color until the pixel is saturated. Here's how it works:

- **Gaussian weight per pixel.** For each splat, compute how much it influences a given pixel using the splat's mean, the inverse 2D covariance, and the pixel coordinates. Multiply that weight by the splat's opacity to get alpha
- **Saturation tracking.** We start with a total weight of 1 and multiply by (1 - alpha) after each splat. Once this drops below a tiny threshold (0.0001), the pixel is considered done and we stop. This prevents splats buried behind others from wasting compute
- **Tile-based rendering.** Rather than checking every splat against every pixel, the image is divided into 16x16 tiles. Each splat's precomputed bounding box (from the radius calculation in Part 2) tells us which tiles it can affect, so we skip everything else
- **From sparse to dense.** The initial render looks like a sparse point cloud since we initialize all scales to 0.01. In practice, the backward pass (not covered here) grows the scene to hundreds of thousands of splats by splitting large ones and cloning into underpopulated areas

I also wrote an equivalent CUDA kernel using PyTorch's inline CUDA compiler as a bonus. Each thread handles one pixel, loops through the relevant splats, and accumulates color the same way the Python version does, just orders of magnitude faster.

The full code for both the Python and CUDA implementations is on GitHub. Between the three parts you should have a solid understanding of what's happening in the forward pass of a Gaussian Splatting renderer, from raw COLMAP points all the way to rendered pixels.

Read the full article on Medium: [A Python Engineer's Introduction to 3D Gaussian Splatting (Part 3)](https://medium.com/data-science/a-python-engineers-introduction-to-3d-gaussian-splatting-part-3-398d36ccdd90)
