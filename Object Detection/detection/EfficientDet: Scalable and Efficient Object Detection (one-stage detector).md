#  EfficientDet: Scalable and Efficient Object Detection (one-stage detector)

[[arxiv-CVPR2020]](https://arxiv.org/abs/1911.09070) [[github_official]](https://github.com/google/automl/tree/master/efficientdet) ~~[[github_model_weight]](https://github.com/zylo117/Yet-Another-EfficientDet-Pytorch)~~ **<u>[[github_model]](https://github.com/signatrix/efficientdet/blob/master/src/model.py)</u>**

- [ ] NAS_FPN: neural architecture search to **automatically design feature network topology**.

![pic](https://pic3.zhimg.com/80/v2-abd814cf370603d2d3e90bfc21ae7d0d_1440w.jpg)

- 任务
  - build a **scalable detection architecture** with both higher accuracy and better efficiency **across a wide spectrum of resource constrain**(e.g. from 3B to 300B FLOPs)

## Efficient multi-scale feature fusion

- weighted bi-directional feature pyramid network (BiFPN) **(repeated blocks)** **(neck)**

  - learnable weights to learn the importance of different input features.

  - ![pic](https://picb.zhimg.com/80/v2-225cc89e2308de82aa2267a9a944762f_1440w.jpg)

  - Optimization for cross-scale connections from **PANet**

    - Remove those nodes that only have one input edge.

      (**left.1, right 5 in PANet**);

    - Add an extra edge from the original input to output node if they are at the same level **(skip connection)**;

- Weighted feature fusion (**Attention mechisiam**)

  - Fast normalized fusion (Relu for w<sub>i</sub>)
    $$
    O=\sum_{i}{\frac{w_i}{\epsilon + \sum{w_i}}} \cdot I_i, w_i \geq 0
    $$

- [Question about FLOPS between BiFPN and FPN](https://github.com/google/automl/issues/284)

  - **Depthwise separable convolution** for feature fusion.

  - Batch normalization and activatation after each convolution.

  - ```python
    class ConvBlock(nn.Module):
        def __init__(self, num_channels):
            super(ConvBlock, self).__init__()
            self.conv = nn.Sequential(
                nn.Conv2d(num_channels, num_channels, kernel_size=3, stride=1, padding=1, groups=num_channels),
                nn.Conv2d(num_channels, num_channels, kernel_size=1, stride=1, padding=0),
                nn.BatchNorm2d(num_features=num_channels, momentum=0.9997, eps=4e-5), nn.ReLU(),
                Swish())
    
        def forward(self, input):
            return self.conv(input)
    ```

  - FPN vs. BiFPN

    - ![pic](https://picb.zhimg.com/80/v2-d278fe61cb26673ea3a442f23ecb8b27_1440w.jpg)

- ~~<u>About code (pytorch)</u>~~

  - ~~param <u>first_time</u> in class BiFPN(nn.Module)~~
    - ~~Whether the input comes directly from the efficient net; if true, **downchannel** it first, and **downsample** P5 to generate P6, P7.~~
    - ~~code里对P4， P5在down-directional 和 up-directional上分别做了downchannel，个人认为不是很有必有.~~

## Modeling scaling method

- EfficientDet architecture

  - ![pic](https://pic4.zhimg.com/80/v2-680448fbf8cf5de53fa7b6a7b0a41f6c_1440w.jpg)

- 

- compound scaling method

  - jointly scale up the resolution / depth /width for all backbone, feature network, box / calss prediction network (coefficient &phi;).

  - **BiFPN network**

    - W &rarr; channels, D &rarr; layers (1.35 is chosen with grid search);

    - $$
      W_{bifpn} = 64 \cdot (1.35^\phi), D_{bifpn} = 3 + \phi
      $$

  - **Box / class prediction network**

    - Fix the width to be the same as BiFPN
      $$
      W_{pred} = W_{bifpn}
      $$

    - $$
      D_{box} = D_{class} = 3 + \lfloor \phi \rfloor
      $$

  - **Input image resolution**

    - $$
      R_{input} = 512 + \phi \cdot128
      $$

  - 

  - ![pic](https://github.com/ganjf/object-detection/blob/master/picture/efficientDet.png?raw=true)

## Experiment

![pic](https://pic3.zhimg.com/80/v2-0d41ba1809e8da3ff526c5b73c2e9cd4_1440w.jpg)

## Conclusion

从论文上来看，BiFPN较FPN而言，可以获得更大的收益；其次Efficient-D0 ～ D7的模型可以进行应用，但是compound scaling method难以迁移到其他模型上去，它对超参数的选择需要以强大的TPU为依托。
