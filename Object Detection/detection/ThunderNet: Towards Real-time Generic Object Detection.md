

## ThunderNet: Towards Real-time Generic Object Detection

paper：https://arxiv.org/pdf/1903.11752.pdf [ICCV 2019]

- **Contribution**

  - Propose  a **lightweight two-stage** generic object detector, **ThunderNet**.
  - Propose a **lightweight backbone**for object detector, **SNet**.
  - Design **Context Enhancement Module (CEM)** and **Spatial Attention Module (SAM)** to eliminate the performance degradation included by small backbones and small feature maps.
  - Investigate the balance between the input resolution, the backbone, and the detection head.

-  **Context Enhancement Module (CEM)**

  - Combine the feature maps from multiple scales to leverage local and global context information.

- **Spatial Attention Module (SAM)**

  - Use the information learned in RPN to refine the feature distribution in RoI warping.

- **ThunderNet**

  - ![](https://gitee.com/gggqq/src/raw/master/ThunderNet.png?raw=true)
  - Large-backbone-small-head design is **<u>better</u>** than the small-backbone-large-head design for lightweight two-stage detectors.
  - Backbone Part (SNet)
    - The input resolution should match the capacity of backbone. (Large backbone with large inputs).
    - **Receptive field: **<u>A large receptive field</u> can leverage more context information and encode long-range relationship between pixels more effectively. <u>Especially for the localization of large objects. </u>
    - **Early-stage and late-stage features: **Generally, localization is sensitive to low-level features (early-stage) while high-level features (late-stage) are crucial for classification. <u>Early-stage features are more important because localization is more difficult.</u>**<u>Increasing the channels in early stages encodes more detail information is beneficial for localization</u>**
    - <img src="https://gitee.com/gggqq/src/raw/master/SNet.png?raw=true&quot; style=&quot;zoom:50%;" style="zoom:50%;" />
    - **ShuffleNetV1:** https://www.jianshu.com/p/29f4ec483b96
      - Group Concolution. 将convolution分为 g 个独立的组, 分别计算.
      - <img src="https://img-blog.csdnimg.cn/20190716102721183.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTAxMjM5OQ==,size_16,color_FFFFFF,t_70" style="zoom:50%;" />
    - **ShuffleNetV2**：https://zhuanlan.zhihu.com/p/48261931
      - <img src="https://pic2.zhimg.com/80/v2-83d494ec03595ac2af9c8c933ee7804d_1440w.jpg" style="zoom:50%;" />
      - **Channel split**: 将输入特征在通道维度分成两个分支，通道数分别为 c' 和 c - c'，一般取 c' = c / 2. （**Channel Concate用来替代 ResNet 中 ShortCut 的 Add**）.
      - 1x1卷积进行平衡输入和输出的通道大小;
      - 组卷积要谨慎使用，注意分组数;
      - 避免网络的碎片化, 减少模型的并行度 ("多路"结构) ;
      - 减少元素级运算; (比入RELU 和 Add，有着较小的FLOPs，较大的MAC) （**FLOPs**: multiply-add num; **MAC**: memory access cost).
      - <img src="https://pic4.zhimg.com/80/v2-8845ef10f6f5da34fa5e7c53cc7efc3f_1440w.jpg" style="zoom:50%;" />

  - Detection Part

    - Compressing RPN and Detection Head: replacing the original 256-channel 3x3 convolution with 5x5 depth-wise convolution and a 256-channel 1x1 convolution.

    - **Context Enhancement Module: **
      - **Enlarge the receptive field**. Aggerate **multi-scale local context information and global context information** to generate discriminative features.
      - Prior FPN involves many extra convolutions and multiple detection branch, which increase the computational cost and induces enormous runtime latency.
      - <img src="https://github.com/ganjf/src/blob/main/Context%20Enhancement%20Module.png?raw=true" style="zoom:40%;" />
    - **Spatial Attention Module**
      - <u>Use the knowledge from RPN to refine the feature distribution of the feature map</u>. To distinguish foreground features from background features.
      - <img src="https://github.com/ganjf/src/blob/main/Spatial%20Attention%20Module.png?raw=true" style="zoom:50%;" />

