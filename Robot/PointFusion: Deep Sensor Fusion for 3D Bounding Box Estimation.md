## PointFusion: Deep Sensor Fusion for 3D Bounding Box Estimation

[[paper](https://openaccess.thecvf.com/content_cvpr_2018/html/Xu_PointFusion_Deep_Sensor_CVPR_2018_paper.html)] [CVPR 2018]

## **Contribution**

- The model learns to effectively combine the complentary image and depth sensor (lidar data) information.
- The method has no scene or object-specific limitation, as well as no limitations on the kind and number of depth sensor used.

## **Three main components:**

### 1. Off-the-shelf CNN

- **Task:** Extract apperance and geometry features from input RGB image crops.

### 2. Variant of PointNet

- **Task: ** the raw 3D point cloud.
- **PointNet**
  - Avoids lossy input preprocessing such as quantization or projection with raw point cloud.
- **No BatchNorm**
  - BatchNorm effectively reduces the covariance shift in the input feature by eliminating the scale and bias in input, **but for the task of 3D regression, the absoulte numerical value of the point locations are helpful.**
- Instead of STN (Spatial Transformer Network, in Point) to canonicalize the input space, **use the known camera geomerty to compute the canonical rotation matrix R<sub>c</sub>. (Alignement between RGB and 3D point cloud).**

<img src="https://gitee.com/gggqq/src/raw/master/PointFusion_Figure3.png" style="zoom:40%;" align="left"  />

### 3. Fusion sub-network **(a vanilla global architecture and a novel dense fusion network)**

- **Task:** Combines the output of RGB and 3D point cloud to predict 3D bounding box.
- **vanilla global fusion network**
  - **Concatenate the RGB and point cloud feature**, and **directly regress** the  3D locations of the eight corners of the target bounding.
  - Loss function is composed of **L1 loss** and **spatial transformation regularization loss (introduced in PointNet), which enforce the orthogonality of the learned spatial transformm matrix.**
  - **Drawback: ** the variance of the regression target is directly dependent on the paticular scenario.
- **dense fusion network**
  - Using the 3D points as dense spatial achors to improve the 3D box predictions **(Predict the spatial offset instead of regressing directly)** and two scoring functions to select the best prediction.
  - **Two scoring function**
    - Supervised scoring function
      - Directly trains the network to predict if a point is **inside the target boudning box.**
    - Unsupervised scoring function
      - Directly trains the netowrk to assign high confidence to the point that is likely to produce a good prediction. **（Distinguishes those points inside the 3D box or not.)**

<img src="https://gitee.com/gggqq/src/raw/master/PointFusion_Figure2.png" style="zoom:50%;" align="left"  />

