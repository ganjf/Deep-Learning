



## CenterNet: represent objects with center points

[CenterNet: objects as points](https://arxiv.org/abs/1904.07850) [CVPR 2019]

[Github](https://github. com/xingyizhou/CenterNet)

- The state-of-the-art keypoint estimation network  &rarr;  **Hourglass-104**.

- Contribution / Method

  - The detector uses keypoint estimation to find center points and regresses to all other properties, such as size, 3D location, orientation, and even pose. **(Simplify the object detecion problem to standard keypoint estimation problem.)**

  - The method could be extend to other tasks with minor effort, such as 3D object detection, multi-person human pose pose estimation.

- loss function

  - **Keypoint**: The training objective is a penalty-reduced pixel - wise logistic regression with focal loss (same as cornerNet).

    - Use 2D Gaussian Kernel with keypoint to train heatmap. **If two Gaussians of the same class overlap, take the element-wise maximum.  **

      <img src="https://gitee.com/gggqq/src/raw/master/corner_det_loss.png?raw=true" style="zoom: 40%;" />

  - **Offset**: L<sub>1</sub> loss. (2-dimensional vector)

  - **Scale size**：L<sub>1</sub> loss. (2-dimensional vector, s<sub>k</sub> = (h, w))
    $$
    L = L_{det} + \lambda_{size}L_{size} + \lambda_{off}L_{off}.\\
    \lambda_{size} = 0.1, \quad \lambda_{off} = 1
    $$

- Inference
  - Extract the peaks in the heatmap for each category by **detecting all responses whose value is greater or equal to its 8-connected neighbors and keep the top 100 peaks. (Top -100 bbox)**.     <u>(Serves as a succifient NMS alternative, implemented using 3 x 3 max pooling)</u>
  - ![](https://gitee.com/gggqq/src/raw/master/center_bbox.png)

