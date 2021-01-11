[TOC]

# EfficientNet: Rethinking Model Scaling for Convolutional Neural Networks

[[arxiv-ICML 2019 Oral]](https://arxiv.org/abs/1905.11946) [[github_official]](https://github.com/tensorflow/tpu/tree/master/models/official/efficientnet) [[github_pytorch]](https://github.com/lukemelas/EfficientNet-PyTorch)

- [ ] Swish [[arxiv]](https://arxiv.org/abs/1710.05941) [[blog]](https://www.cnblogs.com/makefile/p/activation-function.html)
  - x * torch.sigmoid(beta * x)
- [ ] Squeeze and excitation optimization (SE module) [[blog]](https://www.cnblogs.com/fangpengchengbupter/p/8252759.html)
  - "特征重标定"策略：显式地建模特征通道之间的依赖关系，通过学习的方式来自动获取到每个通道的重要程度，然后依照这个重要程度去提升有用的特征并抑制对当前任务用处不大的特征。
  - paper中的 FC 可以用 Conv 来替代（kernel = （1，1）， stride = 1），即合并了 FC + scale 过程；
  - adaptive_avg_pool2d &raar; conv(k=(1,1), s=1, c=channel * se_ratio) &rarr; swish &rarr; conv(c = channel) &rarr; swish
- [ ] MBConvs (Mobile inverted bottle residual block)
  - Dwise Conv: conv （k = (3, 3), c = 12) &rarr; conv(k = (3, 3), c = 1) + conv(k = (1, 1), c = 12) 
  - Depthwise convolution phase + Pointwise convolution phase

## 单一维度变化对模型的影响

- Depth（repeated MBConvs）: 更深网络可以捕获更丰富且更复杂的特征；但梯度消失问题会使得训练过程变得困难；
- Width（channels）: 更宽的网络可以更细粒度（fine-grain）的特征，且更容易训练；但宽而浅的网络结构很难捕获更深层的特征；
- Resolution: 增大输入image的分辨率可以使网络捕获到image更多细粒度（fine-grain）的模式；但是会使得对计算资源的需求上升；
- **通过分别增大Depth，Width，Resolution三个单一维度来提高网络performance的方法会出现饱和状态；**
- ![picture](https://img-blog.csdnimg.cn/20190604195325503.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTQzODAxNjU=,size_16,color_FFFFFF,t_70)
- ![pic](https://pic2.zhimg.com/80/v2-4c422fc482665ee28024f1c9067201fe_1440w.jpg?source=1940ef5c)

## **多维度融合变化**对模型的影响

- compound scaling method
  - 复合因子 &phi; 用于均匀地缩放网络的width，depth 和 image resolution
  - &phi; 表示可用计算资源；&alpha; , &beta;, &gamma; 是通过grid search得到的常数，表示计算资源在depth，width，resolution上的分配情况；
  - ![picture](https://img-blog.csdnimg.cn/20190604195514314.jpg)
  - ![pic](https://img-blog.csdnimg.cn/20190604195404194.JPG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTQzODAxNjU=,size_16,color_FFFFFF,t_70)

## EfficientNet Architecture

- EfficientNet-B0 baseline network (**mobile inverted bottleneck MBConv (MnasNet)** )![pic](https://img-blog.csdnimg.cn/20190604195547313.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTQzODAxNjU=,size_16,color_FFFFFF,t_70)

- MBConv
  ![pic](https://img2020.cnblogs.com/blog/1399393/202003/1399393-20200305223633513-1052314957.png)
  ![pic](https://img-blog.csdnimg.cn/20200514104510688.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1poYW5nX0NoZW5f,size_16,color_FFFFFF,t_70#pic_center)

## Experiment

- EfficientNet-B1, &alpha; = 1.2, &beta; = 1.1, &gamma; = 1.15;
- Swish activation
- 越大的模型需要越多的正则
  - dropout从0.2至0.5增大，对于EfficientNet-B0 - EfficientNet-B6

![pic](https://github.com/ganjf/object-detection/blob/master/picture/FLOPS%20vs.%20ImageNet%20Accuracy.png?raw=true)

- **In code**

  - params_dict = {

    #### (width_coefficient, depth_coefficient, resolution, dropout_rate)

    'efficientnet-b0': (1.0, 1.0, 224, 0.2),
    'efficientnet-b1': (1.0, 1.1, 240, 0.2),
    'efficientnet-b2': (1.1, 1.2, 260, 0.3),
    'efficientnet-b3': (1.2, 1.4, 300, 0.3),
    'efficientnet-b4': (1.4, 1.8, 380, 0.4),
    'efficientnet-b5': (1.6, 2.2, 456, 0.4),
    'efficientnet-b6': (1.8, 2.6, 528, 0.5),
    'efficientnet-b7': (2.0, 3.1, 600, 0.5),
    'efficientnet-b8': (2.2, 3.6, 672, 0.5),
    'efficientnet-l2': (4.3, 5.3, 800, 0.5),
    }

  - Issue about efficient net parameter is different with param in paper (change &phi;).

    - https://github.com/rwightman/gen-efficientnet-pytorch/issues/40

  - ```python
    def round_filters(filters, global_params):
        """Calculate and round number of filters based on width multiplier.
           Use width_coefficient, depth_divisor and min_depth of global_params.
    
        Args:
            filters (int): Filters number to be calculated.
            global_params (namedtuple): Global params of the model.
    
        Returns:
            new_filters: New filters number after calculating.
        """
        multiplier = global_params.width_coefficient
        if not multiplier:
            return filters
        # TODO: modify the params names.
        #       maybe the names (width_divisor,min_width)
        #       are more suitable than (depth_divisor,min_depth).
        divisor = global_params.depth_divisor
        min_depth = global_params.min_depth
        filters *= multiplier
        min_depth = min_depth or divisor # pay attention to this line when using min_depth
        # follow the formula transferred from official TensorFlow implementation
        # filters = filters * width_cofficinent
        # filters以divisor为单位做四舍五入取整；
        new_filters = max(min_depth, int(filters + divisor / 2) // divisor * divisor)
        if new_filters < 0.9 * filters: # prevent rounding by more than 10%
            new_filters += divisor
        return int(new_filters)
    ```

  - ```python
    def round_repeats(repeats, global_params):
        """Calculate module's repeat number of a block based on depth multiplier.
           Use depth_coefficient of global_params.
    
        Args:
            repeats (int): num_repeat to be calculated.
            global_params (namedtuple): Global params of the model.
    
        Returns:
            new repeat: New repeat number after calculating.
        """
        multiplier = global_params.depth_coefficient
        if not multiplier:
            return repeats
        # follow the formula transferred from official TensorFlow implementation
        return int(math.ceil(multiplier * repeats))
    ```

    
