

采用一些方法使模型有更高的准确度但是不增加模型的复杂度和模型的推理代价

- PS：数据增强应该在图像标准化(normalization)之前完成；

- Library:

  - Albumentations [arxiv](https://arxiv.org/abs/1809.06839v1) [github](https://github.com/albumentations-team/albumentations) [docs](hhttps://albumentations.ai/)
  - Augmentor [arxiv](https://github.com/mdbloice/Augmentor) [github]([ttps://github.com/mdbloice/Augmentor](https://github.com/mdbloice/Augmentor)) [docs](https://augmentor.readthedocs.io/en/master/)
    - Biomedical image augmentation using Augmentor

- 数据增强 [[awesome data augmentation\]](https://github.com/CrazyVertigo/awesome-data-augmentation)

  - Photometric distortion (色泽扭曲)
    
  - 亮度、对比度、色调、饱和度、噪声
    
  - Geometric distortion （几何扭曲）

    - 随机缩放、剪切、翻转、旋转

  - 模拟遮挡 (information dropping) (focus model to learn less discriminative object part)

    - [x] random erase [AAAl 2020] [[arxiv\]](https://arxiv.org/abs/1708.04896) [[github\]](https://github.com/zhunzhong07/Random-Erasing) [[blog\]](https://blog.csdn.net/xuluohongshang/article/details/79000951)
      - 在原图 / 目标区域中随机选择一个矩形区域，使用随机数填充

      - 提高模型的鲁棒性

      - 涉及三个参数：1. erasing probability (p);  2. erasing area ratio (s<sub>l</sub> and s<sub>h</sub>);(决定了模拟的遮挡程度)  3. erasing aspect ratio;

      - 论文中给出的参数建议：

        ![picture](https://img-blog.csdn.net/20180108110649690?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveHVsdW9ob25nc2hhbmc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
      
      - IRE, ORE, I+ORE
      
    - [x] CutOut [[arxiv\]](https://arxiv.org/abs/1708.04552) [[github\]](https://github.com/uoguelph-mlrg/Cutout) [[blog\]](https://blog.csdn.net/weixin_41560402/article/details/106036378)

      - 论文中作者尝试遮挡训练所得激活图中的高激活部分（most discriminative）(adversarial erasing method)，但实际效果和随机选择区域遮挡相近；

      - 随机选择一个固定大小的正方形区域，然后采用全0填充

      - 如果选择的区域越界，仅保留区域内的部分，变相地实现了任意面积的遮挡；但是RE保障了选择的必然是规则矩形
      - RE和CutOut的效果需要在实际任务中尝试，可以二者兼而有之

    - [x] hide-and-seek [[arxiv\]](https://arxiv.org/abs/1811.02545) [[github\]](https://github.com/kkanshul/Hide-and-Seek)

      - 将原图划分为若干份(grids of size s * s)，然后对划分的每一份依概率进行隐藏**（采用整个数据集的均值来填充，也就是在标准化后这部分为0，code中是以0表示）**，可以模拟出RE和CutOut的效果；

      - 对于video而言，以一定帧数为单位划分并以一定概率隐藏；

    - [x] GridMask [[arxiv\]](https://arxiv.org/pdf/2001.04086v2.pdf) [[github\]](https://github.com/Jia-Research-Lab/GridMask) [[blog\]](https://blog.csdn.net/weixin_42096202/article/details/103994237)

      - Key: **避免连续区域的过度删除和保留**

        - ~~控制被删除区域的密度和大小主要靠上面r, d, x, y四个参数控制，其中r是保留区域比率，在论文中是一个固定值。d决定了删除区域的大小，在论文中是一个区间的random值。x, y是删除区域的边长，在论文中是random（0，d-1）的一个值。~~cifar10中r=0.4。

        **（code 此处的random均对于mini-batch内的不同图片而言，同一图片（grid mask）中是相同的）**d表示一个单元的大小，是一个区间内的random值，mini-batch中的每一层均不同；删除部分在该单元内的位置是random的，st_w, st_h表示左上角位置；

        （emmmm, 感觉github上的代码有点问题，与文中叙述不太相符）

        ```python
        l = d * self.ratio
        # grid的删除区域大小
        for i in range(0, hh//d):
            lu = d*i + st_h
            ld = lu+self.l
            lu = np.clip(lu, d*i, d*(i+1))
            ld = np.clip(ld, d*i, d*(i+1))
            for j in range(0, hh//d):
                ru = d*j + st_w
                rd = ru+self.l
                ru = np.clip(ru, d*j, d*(j+1))
                rd = np.clip(rd, d*j, d*(j+1))
                mask[lu:ld:, ru:rd] *= 0
        ```

        

      - ![picture](https://img-blog.csdnimg.cn/20200115183115521.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjA5NjIwMg==,size_16,color_FFFFFF,t_70)

      - 使用GridMask的策略主要有两种，**作者通过实验发现，第二种更有效(code使用策略2）**：
        1.设置一个恒定的概率p，这样我们就有机会对每个输入图像应用gridmask
        2.使gridmask的概率随训练时间线性增加，直到达到一个上界P。

      - grid的最小单元一般不要大于最小目标框太多，否则效果可能不理想。

- Feature map (特征图上进行数据增强) (Regularization method)

  - Dropout 
  - DropConnect
  - DropBlock
  - Spatial Dropout 
  - DropPath
  
- 结合多图像

  - [[blog: Mixup, Cutout, CutMix\]](https://arxiv.org/abs/1905.04899v2)

  - [x] Mix-Up [[arxiv\]](https://arxiv.org/abs/1710.09412) [ICLR 2018]
  
    - Image = 0.9X  + 0.1Y, label = [0.9, 0.1]
  
  - [x] CutMix [[arxiv\]](https://arxiv.org/abs/1905.04899v2) [[github\]](https://github.com/clovaai/CutMix-PyTorch) [ICCV 2019 oral talk]
  
    - 将一部分区域随机填充训练集中的其他数据的区域像素值
  
    - CutMix和CutOut是填充区域像素值的区别
  
    - Mixup和CutMix是混合两种样本方式上的区别，mixup是将两张图按比例进行插值来混合样本，cutmix是采用cut部分区域再补丁的形式去混合图像，不会有图像混合后不自然的情形
  
    - Lambda ~ beta distribution 
  
    - ![picture](https://private.codecogs.com/gif.latex?%5Ctilde%7Bx%7D%3D%5Cmathbf%7BM%7D%5Cbigodot%20x_%7BA%7D&plus;%28%5Cmathbf%7B1%7D-%5Cmathbf%7BM%7D%29%5Cbigodot%20x_%7BB%7D)
  
      ![picture](https://private.codecogs.com/gif.latex?%5Ctilde%7By%7D%3D%5Clambda%20y_%7BA%7D&plus;%281-%5Clambda%20%29y_%7BB%7D)
  
    - ![picture](https://private.codecogs.com/gif.latex?r_%7Bx%7D%5Csim%20%5Ctextrm%7BUnif%7D%280%2CW%29%2C%20r_%7Bw%7D%3DW%5Csqrt%7B1-%5Clambda%20%7D%2C)
  
    - ![picture](https://private.codecogs.com/gif.latex?r_%7By%7D%5Csim%20%5Ctextrm%7BUnif%7D%280%2CH%29%2C%20r_%7Bh%7D%3DH%5Csqrt%7B1-%5Clambda%20%7D)
  
  - [ ] AugMix [[arxiv\]](https://arxiv.org/pdf/1912.02781.pdf) [[github\] ](https://github.com/google-research/augmix) [ICLR 2020]
  - [ ] RandAugment [[arxiv\]](https://arxiv.org/pdf/1909.13719.pdf) [[github\]](https://github.com/tensorflow/tpu/tree/master/models/official/efficientnet) [ICLR 2020]
  
  - [ ] Mosaic [[blog\]]()
  
    - 将四张图片进行随机剪裁，再拼接到一张图上作为训练数据，吩咐了图片的背景，并且变相得提高了batch size；
  
    - ![picture](https://img-blog.csdnimg.cn/20200527200124958.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1ExdTFORw==,size_16,color_FFFFFF,t_70#pic_center)
  
  - [ ] Bilateral bluring
  
  - [ ] 
  
- activation function

  - ReLU, leaky-ReLU

  - [ ] Parametric-ReLU
  - [ ] ReLU6
  - [ ] SELU
  - [ ] Swish
  - [ ] Wish
  
- Normalization of the network activations
  - Batch Normalization
  - [ ] Cross-gpu Batch Normalization (CGBN or SyncBN)
  - [ ] Filter Response normalization (FRN)
  - [ ] Cross-Iteration Batch Normalization (CBN)
  
- Skip-connections
  - Residual connections
  - [ ] Weighted residual connections
  - [ ] Multi-input weighted residual connections
  - [ ] Cross stage partial connections (CSP)
  
- Data imbalance between differnt classes
  - [ ] hard negative example mining (two-stage)

  - [ ] online hard example mining (two-stage)

  - [x] focal loss (one / two stage)

  - [x] label smoothing (used to one-hot representation)
    $$
    y' = (1 - \epsilon)*y + \epsilon*u,\quad
    u可以看作是固定分布的噪声
    $$
    

- Objective function bounding bbox regression

  - [zhihu: IoU, GIoU, DIoU, CIoU](https://zhuanlan.zhihu.com/p/94799295)

  - [x] IoU loss 

    - 考虑预测的边界框和 ground truth 间的IoU

    - loss = -ln(IoU) / loss = 1 - IoU

    - - 如果两个框没有相交，则 IoU=0，没有梯度回传，无法进行学习训练且无法度量两个不相交的bbox之间的距离；

      - IoU无法精准地反应两者的重合度；

        ![picture](https://picb.zhimg.com/80/v2-95449558cb098ff9df8c4d31474bd091_1440w.jpg)

  - [x] GIoU (Generalized Intersection over Union) loss [[arxiv\]](https://arxiv.org/abs/1902.09630) [CVPR 2019]

    - 先计算两个框的最小闭包区域面积A<sub>c</sub>，再计算闭包区域中不属于两个框的区域占闭包区域的比重；

      ![picture](https://www.zhihu.com/equation?tex=GIoU+%3D+IoU+-+%5Cfrac%7B%5Cleft%7C++A_%7Bc%7D+-+U+%5Cright%7C%7D%7B%5Cleft%7C+A_%7Bc%7D+%5Cright%7C%7D)

    - GIoU也是一种距离度量，作为损失函数的话，![[公式]](https://www.zhihu.com/equation?tex=L_%7BGIoU%7D+%3D+1-GIoU)，满足损失函数的基本要求（backpropagated)；1. 当两框完全不相交时，预测框会不断向目标框移动；2. 当两框出现覆盖情况时，GIoU退化为IoU；

    - IoU取值[0,1]，但GIoU有对称区间，取值范围[-1,1]

    - ![picture](https://pic2.zhimg.com/80/v2-71e809433f6b26fcf30b4aeb6578413a_1440w.jpg)

  - [x] DIoU loss (distance iou) [[arxiv\]](https://arxiv.org/abs/1911.08287) （AAAI2020）[[zhihu: DIoU + CIoU\]](https://zhuanlan.zhihu.com/p/112057799)

    - **CIoU, DIoU在同一文章中提出，该文强调框回归三要素：重合面积，中心点距离，长宽比**
    - IoU and GIoU
      - 1. 收敛速度缓慢；2. 回归不准确
      - 惩罚项：度量目标框和预测框之间中心点的归一化距离
        - ![picture](https://www.zhihu.com/equation?tex=%5Cmathcal%7BL%7D_%7BD+I+o+U%7D%3D1-I+o+U%2B%5Cfrac%7B%5Crho%5E%7B2%7D%5Cleft%28%5Cmathbf%7Bb%7D%2C+%5Cmathbf%7Bb%7D%5E%7Bg+t%7D%5Cright%29%7D%7Bc%5E%7B2%7D%7D)
        - b 和 b<sup>gt</sup> 分别表示预测框和ground truth 的中心点，p(.)表示欧式距离，c 表示覆盖两个边界框的最小矩形的对角线长度
      - 特性和优越性
        - DIoU直接最小化两框之间的距离，收敛速度更快
        - DIoU在两框出现包含情况时，仍可以快速回归，不会像GIoU一样退化为IoU loss；
      - 该文提出DIoU应用于nms，

  - [x] CIoU loss 

    - DIoU + 长宽比的一致性（惩罚项）
      - ![pic](https://www.zhihu.com/equation?tex=%5Cmathcal%7BL%7D_%7BC+I+o+U%7D%3D1-I+o+U%2B%5Cfrac%7B%5Crho%5E%7B2%7D%5Cleft%28%5Cmathbf%7Bb%7D%2C+%5Cmathbf%7Bb%7D%5E%7Bg+t%7D%5Cright%29%7D%7Bc%5E%7B2%7D%7D%2B%5Calpha+v)
      - ![pic](https://www.zhihu.com/equation?tex=v%3D%5Cfrac%7B4%7D%7B%5Cpi%5E%7B2%7D%7D%5Cleft%28%5Carctan+%5Cfrac%7Bw%5E%7Bg+t%7D%7D%7Bh%5E%7Bg+t%7D%7D-%5Carctan+%5Cfrac%7Bw%7D%7Bh%7D%5Cright%29%5E%7B2%7D)
      - ![pic](https://www.zhihu.com/equation?tex=%5Calpha%3D%5Cfrac%7Bv%7D%7B%281-I+o+U%29%2Bv%7D)
    - ![pic](https://github.com/ganjf/object-detection/blob/master/picture/IoU%20loss%20exp.png?raw=true)

### Bags of specials [[blog\]](https://blog.csdn.net/weixin_38688399/article/details/106732929)

指 plugin module 或后置方法以些许推理代价的增加换取模型准确率的提升。比如扩大感受野，引入注意力机制，或者增加特征融合容量

- 常用的用于增大感受野的结构

  - SPP （spatial pyramid pooling)（SPM）
    - 输出一维特征向量，不适用于FCN
  - [ ] ASPP
    - dilated convolution operation

  - [ ] RFB

- 注意力机制

  - Channel Attention
    - [ ] Cross-level group normalization
    - [ ] Squeeze-and-exciation (SE)
  - Spatial Attention
    - [ ] Stacked dilated convolution
  - [ ] Squeeze-and-Excitation  (channel-wise attention)
  - [ ] Spatial Attention Module (SAM) (point-wise attention)
    - 不影响模型在GPU上的推理速度
  - Align attention
    - [ ] Deformable Convolution V1, V2 and deformable ROI Pooling
      - 增强卷积中的空间采样位置，带偏移的RoI pooling及目标任务中偏移量的学习
      - https://zhuanlan.zhihu.com/p/77644792 （V1 and V2）
      - https://blog.csdn.net/u014380165/article/details/88072737 （V2, modulated deformable convolution, **RCNN feature mimicking**）

- 特征融合

  - skip connection
  - [ ] hyper-column
  - [x] FPN
  - [ ] SFAM
    - Use SE module to execute channel-wise level re-weighting on multi-scale concatenated feature maps.
  - [ ] ASFF
    - Use softmax as point-wise level re-weighting and then add feature maps of different scales.
  - [ ] BiFPN
    - Multi-input weighted residual connections to execute scale-wise level re-weighting.

- Post-processing method'

  - NMS

  - [ ] soft NMS
    - 解决对象遮挡问题

  - [ ] DIoU NMS
    - add the information of the center point distance to the BBox screening process on the basis of soft NMS. 

## 