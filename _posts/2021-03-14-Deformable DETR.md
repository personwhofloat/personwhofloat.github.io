---
layout: post
title:  "Deformable DETR"
date:   2021-03-14
categories: object-detection
---
# Deformable DETR

paper link: [paper](https://arxiv.org/pdf/2010.04159.pdf)

code link: [code](https://github.com/fundamentalvision/Deformable-DETR)

****
## Introduction 

Even though DETR (Detection Transformer) is a good application of Transformer into Object Detection, it faces some issues: one is that it has a slow convergence rate and requires a long time training (in fact DETR was trained for 500 epochs!), and the second is that it is not a good method for detecting small object (with low APs). Deformable DETR solved both of these by introducing two methods:

-  Deformable Attention Module: which only attends at a local set of pixels surrounding a given point instead of attending to all the pixels in the image. This helps fasten the convergence of DETR, as well as helping DETR to work on earlier stage feature map (bigger map size).

-  Multi-scale Deformable Attention Module: which helps the detector to look at multi-scale feature, making DETR performs better on multi-scale object.

****
## Problem Formation

This paper aims at optimizing the performance of DETR, enable it to work with higher resolution feature as well as fasten their convergence rate. To do so, the problem to tackle are:

- The first goal is to find a way to make less Attention computation. Transformer computation is quadratic to the size of the input (or number of queries). Hence, this will help Transformer to work with feature map with smaller stride.
- The second goal is to enable FPN for DETR.

****
## Proposed Solution

1. Deformable Attention Module
   
   Instead of working on the whole image pixels which have too many queries, the multi-head self attention is re-defined to work on only a vicinity of given pixels only. The formula is: 

2. Multi-scale Deformable Attention Module
   
3. FPN on DETR
   
4. Two-stage Encoder only DETR
