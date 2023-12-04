# Disentangling Monocular 3D Object Detection
By Andrea Simonelli, Samuel Rota Rota Bulò, Lorenzo Porzi, Manuel López-Antequera, Peter Kontschieder

Paper: https://arxiv.org/pdf/2308.09616.pdf

Code: 

## Abstract
In this paper we propose an approach for monocular 3D object detection from a single RGB image, which leverages a novel disentangling transformation for 2D and 3D detection losses and a novel, self-supervised confidence score for 3D bounding boxes. Our proposed loss disentanglement has the twofold advantage of simplifying the training dynamics in the presence of losses with complex interactions of parameters, and sidestepping the issue of balancing independent regression terms. Our solution overcomes these issues by isolating the contribution made by groups of parameters to a given loss, without changing its nature. We further apply loss disentanglement to another novel, signed Intersection-over-Union criterion-driven loss for improving 2D detection results. Besides our methodological innovations, we critically review the AP metric used in KITTI3D, which emerged as the most important dataset for comparing 3D detection results. We identify and resolve a flaw in the 11-point interpolated AP metric, affecting all previously published detection results and particularly biases the results of monocular 3D detection. We provide extensive experimental evaluations and ablation studies on the KITTI3D and nuScenes datasets, setting new state-of-the-art results on object category car by large margins.

## TL;DR;
They propose a new disentangling loss to better separate the backpropagation/contribution of each parameter group (e.g., 2D data, 3D location, 3D size, angle, etc.). They also criticize the 11 recall position of KITTI, proposing to use 40 instead.

## Key ideas
- The disentangling loss: reading the paragraph on the paper is better than my explanation.
- The 11 recall position of KITTI is not good, they propose to use 40 instead.
- This is a two-stage method: 2D detection + 3D detection.

## Notes
- The 3d bbox loss is a huber loss between the projected (2d->3d) predicted 3d bbox and the GT 3d bbox. On this loss the disentangling loss is applied. The huber loss is applied component-wise to each element of the argument matrix (i.e., the eight 3d bbox coordinates).

## Takeaways
- 