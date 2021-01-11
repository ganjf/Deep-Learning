## tHybrid Task Cascade for Instance Segmentation

Paper: https://openaccess.thecvf.com/content_CVPR_2019/papers/Chen_Hybrid_Task_Cascade_for_Instance_Segmentation_CVPR_2019_paper.pdf [ICCV 2019]

Code: https://github.com/open-mmlab/mmdetection

- The success of Cascade R-CNN
  - Progressive refinement of predictions.
  - Adaptive handling of training distributions.

- **Contribution**
  - Propse Hybrid Task Cascade (HTC) for instance segmentation. The key idea is **to improve the information flow by incorporating cascade and multi-tasking at each stage** and **leverage spatial context** to further boost the accuracy.
  - Demonstrate **the spatial contexts** benefit instance segmentation by discriminating foreground objects from background clutters.

- **Hybrid Task Cascade**
  - ![](https://gitee.com/gggqq/src/raw/master/Cascade%20Mask%20R-CNN.png?raw=true)
  - **Multi-task Cascade**
    - **Cascade Mask R-CNN**
      - A mask branch following the architecture of Mask R-CNN is added to each stage of Cascade R-CNN. (Figure 1.a)
      - **Drawback:** and box branch at each are excuted in parallel during training, both taking the bounding box predictions from the preceding stage as input.
    - **Interleaved Execution**
      - Interleaved the box and mask branches. (Figure 1.b)
    - **Mask Information Flow**
      - Propse an information flow between mask branches by feeding the mask features of the preceding stage to the current stage. (Figure 1.c) 
      - <img src="https://gitee.com/gggqq/src/raw/master/multi-stage%20mask%20branches.png?raw=true" style="zoom:50%;" />
  - **Spatial Contexts from Segmentation**
    - In addition to the mid-level features, the author **incorporate higher-level features with global information and lower-level features with local information** for better feature representation. 
    - Fusing the semantic contexts feature into main framework. (Figure 1.d)
    - <img src="https://gitee.com/gggqq/src/raw/master/spatial%20contexts.png?raw=true" style="zoom:50%;" />