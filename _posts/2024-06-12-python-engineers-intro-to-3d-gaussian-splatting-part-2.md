---
title: "A Python Engineer's Introduction to 3D Gaussian Splatting (Part 2)"
date: 2024-06-12
categories: [Computer Vision, 3D Rendering]
tags: [gaussian-splatting, python, neural-rendering, computer-vision]
---

In [Part 1]({% post_url 2024-06-11-python-engineers-intro-to-3d-gaussian-splatting %}) we covered how to take 3D points from a COLMAP scan and project them onto a 2D image plane. Part 2 gets into the actual Gaussian part of Gaussian Splatting: how each splat is represented, how its 3D covariance matrix gets projected to 2D, and how we figure out which pixels each splat actually affects.

Here's what this part walks through:

- **Gaussian parameterization.** Each splat is defined by 11 parameters: 3D coordinates, quaternions (4 values) that becomes a rotation matrix, 3 scale values, and an opacity float. The covariance matrix is decomposed as RSS'R' which guarantees it's always positive semi-definite, avoiding the optimization headaches of trying to learn a raw covariance matrix directly
- **Projecting covariance to 2D.** We know how to project points to 2D from Part 1, but we also need to project the 3D covariance matrix so we know how many pixels each splat actually affects. This uses the EWA Splatting formulation with a Jacobian affine transformation. The z-axis information gets discarded after projection since we only care about the 2D image plane
- **Computing splat radius.** Rather than calculating every splat's influence on every pixel (way too slow), we compute eigenvalues of the 2D covariance and use 3 standard deviations as the radius. This gives us tight bounding boxes for each splat so we only compute strength for pixels that actually matter

The companion notebook (part_2.ipynb on GitHub) has all the code to follow along. By the end you have everything needed for the render step: 2D point positions, colors, 2D covariance and its inverse, sorted depth order, bounding boxes for each splat, and opacity values.

Read the full article on Medium: [A Python Engineer's Introduction to 3D Gaussian Splatting (Part 2)](https://medium.com/data-science/a-python-engineers-introduction-to-3d-gaussian-splatting-part-2-7e45b270c1df)
