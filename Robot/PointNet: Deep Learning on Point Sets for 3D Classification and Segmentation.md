## PointNet: Deep Learning on Point Sets for 3D Classification and Segmentation

## Background

- **Typical convolutional architecture require highly regular input data formats**, like those of image grids or 3D voxels, in order to perform weight share and other kernel optimization.
- In order to convert to regular format, **most researchers typically transform such irregular data to regular 3D voxel grids or collections of images (e.g, views) before feeding them to a deep net architecture. **However, those transformation can **obscure natural invariances of the point cloud data**.(e.g, quantization artifacts ) **(lossy transformation)**
- **From a data point of view, a point cloud is a set of vectors, those including x, y, z, color, intensity etc.**
  - **Unordered.**                                                                                                                                                                           
  - **Interaction among points.** The points are from a space with a distance metric. It means that points are not isolated, and neighboring points from meaningful subset.
  - **Invariance under transformations.** For example, rotating and translaing points all together should not modify the global point clould category or the segmentation of the points. 

<img src="https://gitee.com/gggqq/src/raw/master/PointNet_Fig.png" alt="img" style="zoom:40%;"/>

## PointNet

- A unified architecture that **directly take raw point clouds (a set of points) as input** and output either class label for the entire input or per point segment/part labels for each point of the input.
- The PointNet can be trained to perform **3D object classification, object part segmentation and semantic scene segmentation.**
- **Each point is presented by just its three coordinates (x, y, z).**
- Eavluation on ModelNet40 dataset (classification) , ShapeNet part dataset(part segmentation), Stanford 3D semantic parsing dataset (semantic segmentation in scenes).
- **Blensor Kinect Simulator** to generate point cloud.

<img src="https://gitee.com/gggqq/src/raw/master/PointNet_Figure2.png" style="zoom:50%;" align="left"  />

### Three Main Components

#### 1. Symmetry Function for Unordered Input: The max pooling layer

- Three strategies to make a model invariant to input permutation:
  - Sort input into a canonical order.
    - Require an ordering to be stable w.r.t point perturbation is equivalent to **requring that this map preserves <u>spatial proximity</u> (空间邻近性) as dimension reduces.**
  - Threat the input as a sequence to train RNN, but training data should include all kinds of permutation.
  - **Use a simple symmetry function (exchange the position of items will not change result, e.g. operator * and +) to aggregate the information from each point.**
    - Multi-layer perceptron network  + max pooling to capture different properties of the set.

<img src="https://gitee.com/gggqq/src/raw/master/PointNet_Figure5.png" alt="img" style="zoom:40%;"/>

<u>(PS: Attention method: predict a scalar score from each point feature, then the score is normalized across points by computing a softmax. The weighted sum is then computed on the normalized scores and the point features.)</u>

#### 2. A local and global infor. mation combination structure

- Point segmentation requires a combination **(channel concatenation)** of **local gemoetry** and **global semantics**. (Figure2. Segmenatation Network)

#### 3. Two joint alignment networks (canonical space)

- In order to make the learnt representation by point set be **invariant to those geometric transformations**, such as rigid transformation.

- **Natural solution: **align all input set to a canonical space before feature extraction.

- **Author's solution: predict an affine transformation matrix by a mini-network (T-net in Figure 2. Max pooling + fully connected layers) and directly apply this transformation to the coordinates of input points.** 

  - Add a regularization term to **constrain the feature transformation matrix to be close to orthogonal matrix** to make the optimization more stable. (transformation martix in feature space has much larger dimension)

    ​														$L_{reg} = ||I - AA^T||_F^2$

    Where A is the feature alignment matrix predicted by a mini-network.