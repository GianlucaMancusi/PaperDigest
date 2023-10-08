# Far3D: Expanding the Horizon for Surround-view 3D Object Detection
By Xiaohui Jiang, Shuailin Li, Yingfei Liu, Shihao Wang, Fan Jia, Tiancai Wang, Lijin Han, Xiangyu Zhang

Paper: https://arxiv.org/pdf/2308.09616.pdf

Code: "The code will be available soon."

## Abstract
Recently 3D object detection from surround-view images has made notable advancements with its low deployment cost. However, most works have primarily focused on close perception range while leaving long-range detection less explored. Expanding existing methods directly to cover long distances poses challenges such as heavy computation costs and unstable convergence. To address these limitations, this paper proposes a novel sparse query-based framework, dubbed Far3D. By utilizing high-quality 2D object priors, we generate 3D adaptive queries that complement the 3D global queries. To efficiently capture discriminative features across different views and scales for long-range objects, we introduce a perspective-aware aggregation module. Additionally, we propose a range-modulated 3D denoising approach to address query error propagation and mitigate convergence issues in long-range tasks. Significantly, Far3D demonstrates SoTA performance on the challenging Argoverse 2 dataset, covering a wide range of 150 meters, surpassing several LiDAR-based approaches. Meanwhile, Far3D exhibits superior performance compared to previous methods on the nuScenes dataset. The code will be available soon.

## TL;DR;

## Key ideas
- Use 2D priors to improve the 3D object detection. => for long-range objects, they use adaptive queries extracted from 2D priors.

## Notes
- Input: surround-view images. Output: 3D object detections
- Long-range distances pose challenges such as heavy computation costs and unstable convergence -> They propose to exploit high-quality 2D object priors to generate 3D adaptive queries that complement the 3D (fixed) global queries.
- Surround-view methods can be categorized into: **BEV methods** and **sparse query-based methods**.
- - BEV: converts perspective features to BEV features using a view transformer, then utilizing a 3D detector head to produce the 3D bounding boxes. (HIGH computation for long-range)
  - Sparse query-based methods: intend to learn 3D global object queries from data, following the DETR style. Problem: the global fixed queries cannot adapt to dynamic scenarios and miss targets in long-range detection.
  - Using 2D priors can improve the 3D detector recall, which is usually lower than the 2D one.
  - Previous methods: Sim-MOD, MV2D, have already tried to use 2D priors, but for close-range tasks there are two issues:
  - - inferior redundant predictions due to uncertain depth distribution along the object rays (**???**)
    - larger deviations (**???**) in 3D space as the range increases due to frustum transformation (**???**)
  - Problem: During training, the model exhibits a tendency to overfit on densely populated close objects while disregarding sparsely distributed distant objects.
  - - To address the issues they propose 3D adaptive query generation from 2D proposals. => 2D proposals, depth for each of them, => project them to 3D.
  - In the decoder of the queries: perspective-aware aggregation is employed across different image scales and views.
  - - The **perspective-aware aggregation** learns sampling offsets for each query and dynamically enables interactions with favorable features. For example, distant object queries are beneficial to attend large-resolution features.
  - They design a **range-modulated 3D denoising technique** to mitigate query error propagation and slow convergence.
  - - They feed the model **multi-group noisy proposals** around GT into the decoder and train it to _recover 3D GT for positive ones_ and _reject negative ones_. _The injected level of noise depends on the distances and scales_. 


## Takeaways
- Nowadays, 3D detectors have a notably lower recall than 2D detectors. => use 2D priors!
