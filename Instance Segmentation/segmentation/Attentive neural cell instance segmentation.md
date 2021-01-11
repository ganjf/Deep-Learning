## Attentive neural cell instance segmentation

Paper: https://www.sciencedirect.com/science/article/pii/S1361841518308442

GitHub: https://github.com/yijingru/ANCIS-Pytorch

- **Difficulties in nerual cell instance segmentation**
  - Nerual cells vary in shape and size.
  - Many nerual cells have tiny and slender structures.
  - Neural cells tend to adhere to each other to cell interaction.
  - Nerual cells many have obscure contours, and microscopic images usually have low contrast and background impurities.

- Deep backbone contains rich high-level semantics, while ignoring the shallow ones which are rich in low-level shape and texture information of the objects.

- Attentive instance segmentation model

  ![](https://gitee.com/gggqq/src/raw/master/attentive%20cell%20segmentation.png?raw=true)

  - **<u>Detection module: SSD; Segmentation module: U-Net.</u>**

  - Cell Detection

    - **Feature fusion moudle**

      <img src="https://gitee.com/gggqq/src/raw/master/feature%20fusion%20module.png?raw=true" style="zoom:33%;" />

      - Fuse shallow (fine) and deep (coarse) feature maps to faciliate the detection of same cells.

    - **Detection Attention module**

      <img src="https://gitee.com/gggqq/src/raw/master/attention%20module.png?raw=true" style="zoom:33%;" />

      - To distinguish backgournd and foreground information. (like SE module, channel squeeze and excitation.)

  - Cell segmentation

    ![](https://gitee.com/gggqq/src/raw/master/segmentation%20module.png?raw=true)

    - Seg Attention Uint looks like **spatial attention module (SAM) in ThunderNet.** (sigmoid function to do re-weighting).