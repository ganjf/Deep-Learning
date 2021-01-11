## Scale-Aware Trident Networks for Object Detection

Paper: https://arxiv.org/abs/1901.01892 [ICCV 2019]

Code: https://git.io/fj5vR

- **Contribution**

  - Investigate results about **the effect of the receptive field in scale variation.**

  - Propose Trident Network to deal with scale variation problem for object detection. (Multi-branch structure and scale-aware training) 

    (Generate scale-aware feature maps with dialted convolutions.)

  - Propose TridentNet Fast, which only one major branch via weight-sharing trident-block design.

- **Methods for handling scale variation**

  ![](https://gitee.com/gggqq/src/raw/master/multi_receptive_field.png?raw=true)

  - Scale normalized method (SNIP, SNIPER) (based on image pyramid) [blog](https://zhuanlan.zhihu.com/p/38557212)
  - Multi-level features of different spatial resolutions. (FPN)
    - **The image pyramid (multi-scale training) and the feature pyramid (FPN) method share the same motivation that models should have different receptive field for objects of different scales.**
  - Generate scale-specific features through multiple parallel branches. (TridentNet)

- Results about **the effect of the receptive field in scale variation** (dilated conv)

  -  As the receptive field increase (larger dilation rate), the performance of the detector on small objects drops consistently, but better on large objects. (**strong correlation between receptive fields and scale of objects. **)
  - <img src="https://gitee.com/gggqq/src/raw/master/receptive%20field%20experiment.png?raw=true" style="zoom: 33%;" />

- **Trident Network**

  - <img src="https://gitee.com/gggqq/src/raw/master/TridentNet.png?raw=true" style="zoom: 33%;" />

  - Multi-branch Block

    - <img src="https://gitee.com/gggqq/src/raw/master/trident_block.png?raw=true?raw=true" style="zoom: 33%;" />
    - Typically, **replace the blocks in the last stage of the backbone with trident blocks **since larger strides lead to a larger difference in receptive fields as needed.

  - **Weight sharing among branches**

    - Different branches in one multi-branch block share the same structure (except the dilation rates) and thus make the weight sharing straightforward.
    - **The same parameters are trained for different scale ranges under different receptive fields.**

  - Scale-aware training scheme

    - To reduce the scale mismatching (small objects on the branch with too large dilation), <u>detect objects of different scales on different branches.</u>

      - match region-of-interest (RoI) to the corresponding branch with valid range.
        $$
        l_i \leq \sqrt{wh} \leq u_i
        $$

      - For RPN, select **ground truth boxes** which are valid for each branch range during anchor label assignment.

      - For R-CNN, select **region proposal** which are valid for each branch.

- TridentNet Fast
  
  - To tackle the problem of slow inference speed, **TridentNet Fast only use the middle branch for inference for a three-branch network since its valid range covers both large and small objects.**

