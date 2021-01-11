## 1st Place Solutions for OpenImage2019 - Object Detection and Instance Segmentation
paper: https://arxiv.org/pdf/2003.07557.pdf


- **IoU-Net**

  - **Issue:** 
    - Find the feature which generates a good classification score always predicts a coarse bounding box. We need to find a trade-off way with the two task. (classification and localization)

  - **Soultion: **
    - Introduce an extra head to predict the IoU as the **localization confidence.**
    - Aggerate the localization confidence and the classification confidence together to be the final classification score.

- **In the author's work**

  - <img src="https://gitee.com/gggqq/src/raw/master/Pipeline%20of%20the%20Faster%20RCNN%20with%20DH.png?raw=true" style="zoom:50%;" />

  - It can be deemed to disentangle the information by adding a new branch, essentially reduce the shared parameters of the two tasks. **(detection head disentaglement)**

  - Tackle the **spatial misalignment** between the two object functions in the sibling head with <u>**Decouple Head (DH) decouple**</u>.

    - DH decouples two tasks from the spatial dimension by generating two disentangled proposals for them, which are estimated by the shared proposal. 

      <u>(the features in some salient area may have rich information and the boundary may be good at bounding box regression).</u>

  - **Controllable Margin Loss (CML)**

    - $$
      L_c = |S_o - S +m_c|_+, \quad m_c=0.2 \\
       L_r = |IoU_o - IoU + m_r|+, \quad m_r=0.2
      $$

      S<sub>o</sub> is the classification score in the original detection head and S is the classification score in DH. Same as IoU<sub>o</sub> and IoU.

- **Adj-NMS**

  - 1. Given the detected bounding boxes, filter the boxes via the NMS operator with the threshold=0.5.

    2. Adopt the soft-NMS operator to re-weight the scores of the other boxes
       $$
       w = e^{-\frac{IoU^2}{\sigma}} \quad \sigma=0.5
       $$

- **Model Ensemble**

  - [Ensemble Learning Methods for Deep Learning Neural Networks](https://machinelearningmastery.com/ensemble-methods-for-deep-learning-neural-networks/)
  - [How to Create a Bagging Ensemble of Deep Learning Models in Keras](https://machinelearningmastery.com/how-to-create-a-random-split-cross-validation-and-bagging-ensemble-for-deep-learning-in-keras/)
  - Varying Training Data //不同单模型使用不同的训练数据
    - Random Split
    - k-fold Cross-Validiation
    - Bootstrap / bagging
  - Varing Models // 选择不同的单模型
    - Multiple Training Run Ensemble
    - Hyperparameter Tuning Ensemble
    - Snapshot Ensemble
    - Horizontal Epochs Ensemble
  - Varing Combinations // 模型组合
    - Model Averaging Ensemble
    - Weighted Average Ensemble
    - Stack Generalized (stacking) Ensemble
    - Boosting Ensemble
    - Model Weight Averaging Ensemble

- **Bag of Tricks for Detector** 
  - **Sampling**: Long-tail dsitribution characteristics (calss imbalance)
    - class-aware sampling. (same probobility of having at least one category instance)
  - Elaborate Augmentation ("full class batch")
    - Randomly select a class and obtain one image containing it.
    - **Randomly rotate the image. (Larger rotated variance for class with serverly unbalanced aspect ratio such as "flashlight")**
    - Randomly select a scale to crop the image covering the bounding box of the class.

- Expert Model
  - A detector trained on a subset of the dataset to predict a subset of categories.
  - Two import factors:
    - the selection of positive and the negative categories. （According to confusion matrix, false-positive examples / the least number or the worst-performing category in the validation. / visual similarity, Eucliden distance beween the features of two classes.)
    - the ratio between the positive and the negative categories. (approximate to 1:3 (pos : neg))
- **Anchor Selecting**
  
  - Anchor k-means clustering.
- **Multi-scale testing**