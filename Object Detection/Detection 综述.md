[TOC]

# 目标检测综述

reference:

[Awesome object detection paper](https://handong1587.github.io/deep_learning/2015/10/09/object-detection.html#ohem)

[Aweome object detection Github](https://github.com/amusi/awesome-object-detection)

## BackBone (feature extractor)

Purpose: make a trade-off between speed and accuracy. 

- deeper and densely connectied backbones
  - ResNet
  - ResNeXt
  - AmoebaNet
- lightweight backbones (applyed to mobile devices)
  - MobileNet
  - ShuffleNet
  - SqueezeNet
  - Xception
  - MobileNetV2
  - PeleeNet (PeleeNet with SSD to achieve real-time object detection)

## Detectors

### One-Stage (high inference speed)

- YOLO pipeline
  - divide the input to S x S grid,  where a grid cell is responsible to detect the object whose center falls into.



### Two-Stage (high localization and object recognition accuracy)

- Region Proposal 
  - selective search
  - region proposal network (fully convolutional network)
    - RPN, (proposed in Faster R-CNN)
    - ![picture](https://img2018.cnblogs.com/blog/1414369/201903/1414369-20190323160613631-1810905247.png)
  - 正负样本
    - anchor 与 ground truth 的 IOU 大于某一阈值（如 0.7）则为正样本，若小于某一阈值（如 0.3），则为负样本；

- ROI Operation [RoI pooling and RoIAlign](https://blog.csdn.net/u013066730/article/details/84062027?utm_medium=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.nonecase&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.nonecase)

  - extract a fixed size feature map from region proposals of different size.

    - RoI Wrap: 把原始大尺寸的特征图缩放为指定尺寸的特征图

    - [ ] SPP Net
    - RoI Pooling
      - Traditional RoI pooling quantizes floating-number in two steps to get approximate feature values in each bin.
      - Quantization cause misalignments between the RoI and the extracted features.

    - [ ] ROI Aligin (Mask R-CNN [[paper\]](http://openaccess.thecvf.com/content_iccv_2017/html/He_Mask_R-CNN_ICCV_2017_paper.html))
      - RoIAlign avoids any quantizatin of the RoI boundaries or bins.
      - 双线性插值 + Pooling

- [ ] **Fast R-CNN**

- [x] (NIPS 2015) **Faster R-CNN**: ToTowards Real-Time Object Detection with Region Proposal Networks [[paper\]](https://arxiv.org/abs/1506.01497) [[pytorch\]](https://github.com/jwyang/faster-rcnn.pytorch)

![picture](https://img-blog.csdnimg.cn/20200109131544666.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9icnljZTEwMTAuYmxvZy5jc2RuLm5ldA==,size_16,color_FFFFFF,t_70)

- 模型训练（迭代法） [[ref\]](http://www.paperweekly.site/papers/notes/553)

  - 使用预训练模型初始化共享卷积层并训练RPN

  - 使用上一步得到的RPN参数生成ROI proposal，再使用预训练的模型初始化共享卷积层，训练Fast RCNN部分（classifier部分）
  - 将训练后的共享卷积层参数固定，同时将Fast RCNN的参数固定，训练RPN。（从这一步开始，共享卷积层的参数真正被两大块卷积网络共享）
  - 同样将共享卷积层参数固定，并将RPN的参数固定，训练Fast R-CNN部分。

- 模型测试

  - 首先通过RPN生成约20000（pixels*9）个anchor
  - 对20000个anchor进行第一次边框修正，得到修订边框后的Proposal
  - 对超过图像边界的proposal的边进行clip，使得该proposal不超过图像范围

  - 忽略掉长或者宽太小的proposal

  - 将所有proposal按照前景分数从高到低排序，选取前6000个proposal

  - 使用阈值为0.7的NMS算法排除掉重叠的proposal

  - 针对上一步剩下的proposal，选取前300个proposal进行分类和二次边框修正

- RPN网络

  - 在滑动窗口的每个像素点对应的原图片上设置 K 个anchor ( K = scale_num * ratio_num), 假设feature map的大小为 a x b，则anchor数为a x b x K;

  - 对超过图像边界的anchor进行clip;

  - 给满足以下两种情况的anchor设为正标签（前景）

    - 与某个ground truth 有着最高 IOU 的anchor;
    - anchor 与某个 ground truth 的 IOU 大于 0.7；

  - 给满足以下条件的anchor设为负标签（背景）

    - 与所有ground truth 的 IOU 小于 0.3；

  - cls: 区分前后景；reg: (x, y, w, h)坐标偏差![picture](https://img-blog.csdnimg.cn/20191101161007226.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3lpbmc4NjYxNTc5MQ==,size_16,color_FFFFFF,t_70)；

  - RPN只对有标签部分计算loss, **(cross entropy + smooth L1)**

    - ![picture](https://img-blog.csdnimg.cn/20181108105638545.png)
    - N_cls表示采样anchor数；N_reg在论文中是feature map的size，即未经采样的全部anchor数，λ=10；code中取为采样的正负样本数总和(256)，此时λ=1；
    - 在一个图像中随机抽取256个anchor来计算loss，其中采样的正、负anchor比例为1：1；如果一个图像中有少于128个正样本，则用负样本填充；
    - IOU balanced sampling.
      - 核心操作是对负样本按照 IOU 划分K个区间，每个区间再进行随机采样，保证易学习负样本和难负样本尽量平衡。
      - 首先按照iou分成k个区间，先尝试在不同区间进行随机采样采相同多数目的样本，如果不够就全部采样；进行一轮后，如果样本数不够，再剩下的样本中均匀随机采样。

    

- (ICCV 2017)**Mask R-CNN** [[paper\]](http://openaccess.thecvf.com/content_iccv_2017/html/He_Mask_R-CNN_ICCV_2017_paper.html)



## FPN (Feature Pyramid Networks)

- [ ] (ICCV 2017) A backbone extracts RoI features from different levels of the feature pyramid according to their scale. [CSDN](https://blog.csdn.net/u014380165/article/details/72890275)  [paper](https://arxiv.org/abs/1612.03144)

- ![picture](https://img-blog.csdn.net/20170606223026911?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxNDM4MDE2NQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
- ![picture](https://img-blog.csdn.net/20170606222647362?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxNDM4MDE2NQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
- 作者认为足够低层的信息对于检测小物体是很有用的，并通过上采样和低层信息融合，并在每层做独立的预测。
- 1 x 1的卷积来实现skip connection.

## NMS (non-maximium suppression)

分类器会给每个bounding box 计算出属于每一类的概率score.

概述：对于一系列重合程度较高的bb，我们选择其中score最高的一个;

- 对于每一类，首先把所有score < thresh的bb的score设为0；
- 将所有的bb按照得分排序，选中最高分及其对应的bb;
- 遍历其余的bb，如果和当前最高分的bb的重叠面积（IOU）大于一定的阈值，便将该bb删除
- 从未处理的bb中继续选择一个最高分的bb，重复上述过程;

## Batch Normalization ([BN](https://www.zhihu.com/question/38102762))

- ![12](https://latex.codecogs.com/svg.latex?y_i%20=%20\gamma_i%20*%20\hat{x_i}%20+%20\beta_i)
- “scale and shift"使得加入的BN能够有可能还原到最初的分布，保证模型的capacity.
- 在CNN中，BN层应作用在非线性映射前，即对 x  = Wu + b做规范化
- 目的：客服深度神经网络难以训练的弊病；
- internal Covariate Shift
  - covariate shift
    - 概念：源空间和目标空间的条件概率是一致的，但是边缘概率不同
  - 对于神经网络的各层输出，由于经过层内操作作用，其分布和各层对应的输入信号分布不同，而且差异会随着网络深度增大而增大，但是其暗指的样本标记是不变的。
- 什么时候使用BN
  - 网络收敛很慢或者 梯度爆炸 / 梯度弥散 无法先训练；
  - 一般情况下也可以加入BN来加快训练速度，提高模型精度；
- 缺点：
  - BN很依赖于batch的大小，当batch值很小时，计算的均值和方差很不稳定；
  - ResNet, batch=16->batch=8, 性能出现明显下降；