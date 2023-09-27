# Learning Auxiliary Monocular Contexts Helps Monocular 3D Object Detection
By Xianpeng Liu, Nan Xue and Tianfu Wu

https://arxiv.org/pdf/2112.04628v1.pdf
## Abstract
Monocular 3D object detection aims to localize 3D bounding boxes in an input single 2D image. It is a highly challenging problem and remains open, especially when no extra information (e.g., depth, lidar and/or multi-frames) can be leveraged in training and/or inference. This paper proposes a simple yet effective formulation for monocular 3D object detection without exploiting any extra information. It presents the MonoCon method which learns Monocular Contexts, as auxiliary tasks in training, to help monocular 3D object detection. The key idea is that with the annotated 3D bounding boxes of objects in an image, there is a rich set of well-posed projected 2D supervision signals available in training, such as the projected corner keypoints and their associated offset vectors with respect to the center of 2D bounding box, which should be exploited as auxiliary tasks in training. The proposed MonoCon is motivated by the Cramer-Wold theorem in measure theory at a high level. In implementation, it utilizes a very simple end-to-end design to justify the effectiveness of learning auxiliary monocular contexts, which consists of three components: a Deep Neural Network (DNN) based feature backbone, a number of regression head branches for learning the essential parameters used in the 3D bounding box prediction, and a number of regression head branches for learning auxiliary contexts. After training, the auxiliary context regression branches are discarded for better inference efficiency. In experiments, the proposed MonoCon is tested in the KITTI benchmark (car, pedestrain and cyclist). It outperforms all prior arts in the leaderboard on car category and obtains comparable performance on pedestrian and cyclist in terms of accuracy. Thanks to the simple design, the proposed MonoCon method obtains the fastest inference speed with 38.7 fps in comparisons

## TL;DR;
They predict all the information to locate a bounding box in 3D, but they also exploit the ground truth 3D bounding boxes available during training, projecting them into 2D using the intrinsic camera matrix and asking the model to also predict these auxiliary 2D information, such as the projected 2D corners of the 3D bounding box.

## Key ideas
- The auxiliary tasks, which involve projecting 3D bounding box data into 2D, enhance the model's robustness by providing it with additional data to predict.
- 

## Notes
- Given that in monocular 3D object detection, we have only the image without any extra data, the paper proposes to enrich the supervision signal by exploiting the most from the _annotated_ 3D bounding boxes. --> During training, you can project 2D annotated corners of the bounding box, and the offset vectors of the center of the 2D bounding box. It is an **auxiliary task**.
- Simple design: DDN backbone, regression heads for 3D bounding box (Train/Test), several regression head branches for learning auxiliary contexts (Train only).
- They use **heatmaps** to locate bounding boxes on the 2D space and then later project them in 3D using the depth map using the intrinsic matrix.
- Only tested on KITTI (car, pedestrian, and cyclist)
- In KITTI, the angle is $\alpha \in [−\pi, \pi]$. Other info to predict: $(x,y,z)$ and $(h,w,l)$, all measured in the camera coordinate system.
- The AP based on IoU is less sensitive to the shape and orientation of the bbox. *The AP will drop catastrophically if the 3D center location is perturbed!*
- Inference heads (for each head, they use the same lightweight model $g(\cdot;\Phi)$, different weights):
  - $c \times h \times w$ the 2D heatmap to locate targets of $c$ different classes.
  - $2 \times h \times w$ for the 3D bounding box center $(x_c,y_c)$ in the image space (they predict a heatmap).
  - $1 \times h \times w$ for the depth map (they use an inverse sigmoid transformation to handle the unbounded output)
  - $1 \times h \times w$ for the uncertainty in the depth estimation.
  - $3 \times h \times w$ for the shape dimension of the bounding box
  - $2b \times h \times w$ for the observation angle $\alpha$. The angle range $[-\pi,\pi]$ is divided evenly into a predefined number of $b$ non-overlapping bins (e.g., $b=12$). They predict the bin index $\alpha_i \in {0,1,\dots,11}$ from the first $b$ channels (using ``argmax`` after softmax along the $b$ channels) and the corresponding angle residual, $\alpha_r$ in the second $b$ channels. With proper conversions to ensure that $\alpha \in [-\pi,\pi]$
- Computing the 3D bounding box:
  1. Apply NMS on the resulting heatmap to locate bounding box centers $(x_b, y_b)$.
  2. The projected 3D center is obtained as $(x_c,y_c) = (x_b+\Delta{x_b},y_b+\Delta{y_b})$.
  3. The depth is predicted by the network, and with the camera intrinsic matrix, we can extract the 3D location $(x,y,z)$
  4. $(h,w,l)$ are predicted by the network as well as the angle $\alpha$.
- **Auxiliary Context Regression Heads**:
  - $9 \times h \times w$ they predict a heatmap for the 9 projected key points: 8 corner points of the projected 3D bounding box + the center heatmap
  - $16 \times h \times w$ they regress the 8 projected corner points (from the 2D bounding box center to the 8 projected corner points of the 3D bounding box) (2 values each)
  - $2 \times h \times w$ they predict the _height_ and _width_ of the 2D bounding box.
  - (Heatmap-related problem) They predict the quantization error made by the backbone downscaling:
    - $2 \times h \times w$ they regress the residual of the 2D bounding box center
    - $2 \times h \times w$ they regress one map for the residual of the 9 projected key points (keypoint-agnostic way for simplicity)
- Loss functions:
  - **Gaussian kernel weighted focal loss**: for the heatmaps (as used in CenterNet).
  - **Laplacian aleatoric uncertainty loss**: for the depth estimation.
  - **Dimension-aware L1 loss**: for the shape dimensions.
  - **Cross-Entropy loss**: for the bin index in observation angles.
  - **L1 loss**: for offset vectors, the intra-bin angle residual, 2D bounding box sizes, and quantization residual.
  - Overall loss functions: weighted sum of all terms. They set all the terms to $1.0$ except for the 2D size L1 loss which uses 0.1.
## Takeaways
- Learning additional auxiliary contexts increases model robustness: the auxiliary contexts are a set of **well-posed** supervision signals available during training.
- One main challenge of improving monocular 3D object detection's overall performance is in accurately inferring the 3D center location.
- In practice, the 3D center location $(x, y, z)$ is often decomposed to the projected 3D center in the image plane $(x_c, y_c)$ and the object depth z. With the camera intrinsic matrix assumed to be known in training and inference, the 3D location can be recovered with the inferred projected 3D center and object depth.
- [**Anchor-offset formulation**] Is suggested to predict some additional offset $(\Delta{x}_b^c,\Delta{y}_b^c)$ for the 2D center $(x_b, y_b)$ to properly locate the projected 3D bounding box center $(x_c,y_c)$ in the image plane.
- info: DLA-34 is widely used for fair experiment comparisons in monocular 3D object detection.
