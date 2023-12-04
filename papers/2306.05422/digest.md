# OmniMotion: Tracking Everything Everywhere All at Once
By Qianqian Wang, Yen-Yu Chang, Ruojin Cai, Zhengqi Li, Bharath Hariharan, Aleksander Holynski, Noah Snavely

Paper: https://arxiv.org/pdf/2306.05422.pdf

Code: http://omnimotion.github.io/

Venue: ICCV 2023

## Abstract
- We present a new test-time optimization method for estimating dense and long-range motion from a video sequence. Prior optical flow or particle video tracking algorithms typically operate within limited temporal windows, struggling to track through occlusions and maintain global consistency of estimated motion trajectories. We propose a complete and globally consistent motion representation, dubbed OmniMotion, that allows for accurate, full-length motion estimation of every pixel in a video. OmniMotion represents a video using a quasi-3D canonical volume and performs pixel-wise tracking via bijections between local and canonical space. This representation allows us to ensure global consistency, track through occlusions, and model any combination of camera and object motion. Extensive evaluations on the TAP-Vid benchmark and real-world footage show that our approach outperforms prior state-of-the-art methods by a large margin both quantitatively and qualitatively. 

## TL;DR;
- 

## Key ideas
- 

## Notes
- **Goal**: Estimating full-length motion trajectories for every pixel in every frame of a video at test-time.
- **Literature problems**: sparse feature tracking and dense optical flow can't model the motion of a video: pairwise optical flow fails to capture motion trajectories over long temporal windows, and spare tracking does not model the motion of all pixels. Locality of previous works results in accumulated errors over time. Even when prior methods do consider long-range context they operate in the 2D domain, resulting in a loss of tracking during occlusion events. Problems: long sequences, occlusions.
- **Input**: a video sequence of $T$ frames, and a pair-wise optical flow estimates between each pair of consecutive frames.
- **Omnimotion representation**: 



## Takeaways
- 
