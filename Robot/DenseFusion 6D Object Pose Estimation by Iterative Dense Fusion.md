## **DenseFusion**: 6D Object Pose Estimation by Iterative Dense Fusion

> **Heterogeneous architecture: DenseFusion,** processes the two data sources individually and uses a novel dense fusion network to extract **pixel-wise dense feature embedding**, from which the pose is estimated.
>
> **The autors propse an iterative method which performs pose refinement within the end-to-end learning framework.**

## Releated Work

- PoseCNN: with a highly customized Iterative Close Point (ICP).
- MCN: with multi-view hypothesis verification  scheme.
- Prediction of 2D keypoint with PnP to solve the poses.
- Frustrum PointNet and PointFusion: achieve good performance in driving scenes and the capacity of real-time inference. **But fall short under heavy occlusion, which is common in manipulation domains.**

 ## Contribution

- **Propose an end-to-end deep learning approach** for estimating 6-DoF pose known objects from RGB-D inputs. 
- Use a novel **dense fusion network** to extract **pixel-wise dense feature embedding**, from which the pose is estimated.
- Propose an **iterative method which performs pose refinement** within the end-to-end learning framework.

## DenseFusion

<img src="https://gitee.com/gggqq/src/raw/master/DenseFusion_Figure2.png" style="zoom:50%;" align="left"  />

- **Represent 6D poses as a homogeneous transformation matrx , p = [R | t].** Since the 6D poses of the objects are estimated from camera images, the poses are defined with respected to the camera coordinate frame. <u>(Each object has its own world coordinate, taking the center of gravity as origin)</u>
- **Input: color image and masked depth pixels (converted to 3D point cloud).**
- Seperately to generate color and geometric features from embedding spaces that **retain the instrinsic structure of the data sources.**

### Four Main Components
#### 1. Semantic Segmentation

- Using an encode-decoder architecture that takes an image as input and **generate an N+1-channelled semantic segmentation map**. **Each channel is a binary mask depict objects of each of the N possible known classes.** 

- Using segmentation to achieve object detection.

   [Reference: Y. Xiang, T. Schmidt, V. Narayanan, and D. Fox, “Posecnn: A convolutional neural network for 6d object pose estima- tion in cluttered scenes,” Arxiv preprint arxiv:1711.00199, 2017.]

#### 2. A fully convolutional network

- Process the color information and maps each pixel in the image crop to a color feature embedding.
- The image embedding network is a CNN-based encoder-decoder architecture. (**d<sub>rgb</sub> - dimensional embedding space**)


### 3. PointNet-based network
- Process each point in the masked 3D point cloud to a geometric feature embedding.
- **Propose a geometric embedding network that generates a dense per-point feature by mapping each of the P segmented points to a d<sub>geo</sub> - dimensional embedding space.**
- Using average-pooling instead of max-pooling in PointNet.


### 4. A pixel-wise fusion network
- Combines both embeddings and outputs the estimation of the 6D pose of the object based on an unsupervised confidence scoring.

- **Perform local per-pixel fusion instead of global fusion,so that predictions can based on each fused feature.**

  - Due to heavy occlusion and segmentation errors, the set of features may contain features of points/pixels on other objects or parts of the background.

- With local per-pixel fusion, **we can potentially select predictions based on the visible part of the object and minimize the effects of occlusion and segmentation noise. (associate the geometric feature point with image feature point using camera intrinsic parameters)**.

- Train the network to predict one pose from each densely-fused feature. So that the result is a set of P predicted poses, one per feature, with a confidence score for each prediction pose. (for each object).

- **Learning objective (loss function)**

  - For asymmetric objects, define the pose estimation loss as the distance, **where x<sub>j</sub> denotes the j<sup>th</sup> point of M randomly selected 3D point form the object's 3D model (asymmetric object has a unique canonical frame).** 

    ​						$L_i^p =\frac{1}{M}\sum_j||(Rx_j+t) - (\hat{R_i}x_j+\hat{t_i}||)$ 

  - For symmetric objects, it has more than one and even an infinite number of canonical frames, **so  we instead minimize the distance between each point on the estimated model orientation and the closet point on the ground truth model.**

    ​						$L_i^p =\frac{1}{M}\sum_jmin_{0<k<M}||(Rx_j+t) - (\hat{R_i}x_j+\hat{t_i}||)$

  - Learn to balance the confidence among the per dense-pixel predictions, **where N is the number of randomly sampled dense-pixel features from the P elements of the segment,** w is the balance factor.

    ​						$L=\frac{1}{N}\sum_i(L_i^pc_i-wlog(c_i))$

### 5. An iterative self-refinement method
- To train the network in a curriculum learning manner and refine the estimation result iteratively.
- **Pre: Iterative closest point algorithm (ICP) used by many 6D pose estimation methods.**

- Key Procedure (Each step, training minbatch once + K iteration to refine)

  - **Consider the previously predicted pose as an estimate of canonical frame of the target object and transform the input point cloud into this estimated canonical frame.**

  - **Feed the transformed point cloud back into the network and predict a residual pose based on the previously estimated pose.** (类似于EM算法)

  - At each iteration, reuse the image fearture embedding from the main network to perform dense fusion.

  - After K (K=2 in exp) iterations, final pose estimation as the concatenation of the per-iteration estimations:

    ​					$\hat{p}=[R_K|t_K]·[R_{K-1}|t_{K-1}]·····[R_0|t_0]$

<img src="https://gitee.com/gggqq/src/raw/master/DenseFusion_Figure3.png" style="zoom:50%;"/>















