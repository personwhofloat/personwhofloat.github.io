---
layout: post
title:  "CenterNet: Object as Points"
date:   2021-02-25
categories: object-detection
---
# CenterNet: Object as Points

Paper link: [paper](https://arxiv.org/abs/1904.07850)

Code link: [code](https://github.com/xingyizhou/CenterNet)

My Implementation: [code](https://github.com/tson1997/CenterNet-ReImplementation)

While there are many methods of object detection treating the problems as a set of anchor boxes classification, there are ones that treat it as a pixel labeling problem (such as [FCOS](https://github.com/tson1997/Deep-Learning-Paper/blob/main/Object%20Detection/One-Stage/FCOS.md)). CenterNet is also one methods in the class of pixel labeling approaches: it treated the problem as a keypoint (Center) finding and along with width and height of the box, CenterNet can directly output the final bounding box without any post processing such as non-maximal suppression.

**Special points to note:** There are two models named CenterNet, one is this model, and the other is the model applying corner pooling to find keypoint triplets (corners and center.)

My overall thoughts on this model is that CenterNet:

- Uses only Convolutions to get the final results.
- Has many applications not limited to Object Detection.
- Does not need NMS for postprocessing, hence, reduce inference time.

My impression for CenterNet is that it can be very useful in many tasks, and it has many things in common with FCOS (FCOS treats the problem a little differently).

****
## Problem Formation

Given an image I. The goal is to estimate the set S of center points and sizes of bounding boxes: S = {(c_x, c_y, w, h) for all boxes}. In order to find S, the model finds 3 heat maps:
- The first heat-map of size [H/s]*[W/s]*C where H,W is the height and width of I, C is the number of classes we have to identify, and s is the number of strides in the output. This heat map is responsible for finding all the keypoints in the image. 
  
- The second heat-map of size [H/s]*[W/s]*2. This accounts for the height and width of the objects that we have to find.

- The third heat-mao of size [H/s]*[W/s]*2 accounts for the offset that keypoints in the original image and the strided ones have.
  
****
## Proposed Solution

**1. Convert labeling from bounding boxes label**

a. Center Heat-map

For each bounding box (x0,y0,x1,y1) fall within the class *s*, the center is (c_x, c_y) = ((x0+x1)/2, (y0+y1)/2). We will create a Gaussian map with the mean at the center point, so the value at an arbitrary point p is:

<img src="https://render.githubusercontent.com/render/math?math=Y_{p,s} = \exp\Big(-\dfrac{(p_x - c_x)^2 + (p_y-c_y)^2}{2\sigma_p^2}\Big)">

For the pixels that have multiple value in previous formula (fall into the mutual regions), its value would be the element-wise smallest.

<text style='text-decoration:underline;'>**Personal note*</text>: As such, the model is not really good at detecting overlap objects, but can be brilliant for non-over lapping problem such as Text Detection or layout detection.

b. Offset Heat-map:

<img src="https://render.githubusercontent.com/render/math?math=O_{\tilde{p}} = \dfrac{p}{s} - \tilde{p}">

c. Size Heat-map:

<img src="https://render.githubusercontent.com/render/math?math=S_k = "> (This is not clear in the paper though)

**2. CenterNet Architecture**

Architecture of CenterNet is rather simple: it consists of a *backbone* network that called Down-Sample, a neck (optional) and an *Up-Sample* block accounts for producing predicting heat-maps.

In the paper, the author experimented on 4 backbones: Resnet18, Resnet101, DLA and HourGlass. HourGlass yields best mAP while Resnet18 is the fastest (due to its light network architecture).


**3. Loss Function**

Assume that the output of the network are \hat{Y} for keypoint (center), <img src="https://render.githubusercontent.com/render/math?math=\hat{O}"> for offset, and <img src="https://render.githubusercontent.com/render/math?math=\hat{S}"> for size, and the input image has *N* objects.

For **loss function**, since there are three heatmaps to optimize, we will have 3 losses: <img src="https://render.githubusercontent.com/render/math?math=L_{center}, L_{offset}, L_{size}">, where:

- <img src="https://render.githubusercontent.com/render/math?math=L_{center} = \dfrac{-1}{N} \sum_{xyc}\begin{cases}(1-\hat{Y}_{xyc})^\alpha log(\hat{Y}_{xyc}), \forall Y_{xyc}=1 \\ (1-\hat{Y}_{xyc})^\beta (\hat{Y}_{xyc})^\alpha*log(1-\hat{Y}_{xyc}), else \end{cases}">


- <img src="https://render.githubusercontent.com/render/math?math=L_{offset} = \frac{1}{N}\sum_{xyc}|\hat{O}_{\tilde{p}} - (\dfrac{p}{s} - \tilde{p})|">


- <img src="https://render.githubusercontent.com/render/math?math=L_{size} = \frac{1}{N}\sum_{k=1}^{n}|\hat{S}_k - s_k|">

We saw that loss for center is the focal loss (to balance out the imbalance in center-background sets of points), loss for offset and size are just simply smooth L1_loss.

<img src="https://render.githubusercontent.com/render/math?math=L = L_{center} + \lambda_{offset}*L_{offset} + \lambda_{size}*L_{size}">

****
**Technical note**
- In the repositories, they have an installation for Deformable Convolution v2, it may be a hint for different backbone than the ones mentioned in the paper.
- The author did not implement Feature Pyramid Network. Instead, they sum-up all the layer feature. This may be okay since they will not need any NMS at inference time, but the result may not be the best.
