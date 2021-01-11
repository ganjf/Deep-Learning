## HAR-Net: Joint Learning of Hybrid Attention for Single-Stage Object Detection

paper: https://ieeexplore.ieee.org/document/8931260 (IEEE 2019)

- **Aim of the paper**
  - Using Attention mechansim to tackle the class imbalance issue and imprecise feature representation.
  - To incorporate visual attention with single - stage object detection. (spatial attention, channel attention, align attention).
- **Contribution**
  
- Integrate the hybrid attention into Retina-Net and study weight sharing of attention modules in pyramidal paradigm. 
  
- Spatial Attention: **stacked dilated convolution**
  - To re-weight feature response maps in larger receptive filed.
  - The last convolutional layer outputs a 1-channel mask with attention scores; with sigmoid activation, the module generates a feature map with each pixel indicating the relative **spatial importance**.
  - <img src="https://gitee.com/gggqq/src/raw/master/stacked dilated convolution.png?raw=true" style="zoom:50%;" />
- Channel Attention: **cross-level group normalization(CLGN)**, **squeez-and-excitation(SE)**
  - Adaptive feature scaling with the cross-level global and local features.
  - Viewed as the selection of feature response maps for high-level abstraction on the demand of object detection. The weights indicate the channel-wise feature importance.        
  - **cross-level group normalization (CLGN)**
    - The normalization imposed for groups of channels across the pyramid to estimate the relative importance. Feature scaling is applied after the CLGN for channel re-weighting.
  - <img src="https://gitee.com/gggqq/src/raw/master/pyramid-sharing channel attention.png?raw=true" style="zoom:50%;" />
- Align Attention: **deformable convolution**
  - Pyramidal feature alignment.
  - Aim to model the transformations of features from various levels for flexible organization.
  - <img src="https://gitee.com/gggqq/src/raw/master/Aligned attention module.png?raw=true" style="zoom:50%;" />
- HAR-Net archtiecture
  
- <img src="https://gitee.com/gggqq/src/raw/master/HAR-Net.png?raw=true" style="zoom:50%;" />
  
- **Training**

  - Issue of slow convergence: firstly train the main network for better decriptive features; the add the spatial and channel attention modules with serveral more iterative learning. (can obtain minor improvement in accuracy than the end-to-end training method).

- **Inference**

  - Soft-NMS: decays the confidence of all other detected boxes with a continuous function of the IoU.

  - Bounding box voting: 
    $$
    \tilde{R} = \sum_{i}p_iR_i / \sum_i p_i, \quad \tilde{p} = max_i p_i
    $$

  - Multi-scale testing

    - The double-scale testing is quite effective for detecting objects with scattered scales.
    - <img src="https://gitee.com/gggqq/src/raw/master/multi-testing.png?raw=true" style="zoom:50%;" />