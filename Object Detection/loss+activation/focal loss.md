# Focal Loss for Dense Object Detection

Ref: [[paper]](https://arxiv.org/abs/1708.02002) [[github (paper)]](https://github.com/facebookresearch/Detectron) [[focal loss (pytorch)]](https://github.com/yatengLG/Focal-Loss-Pytorch/blob/master/Focal_Loss.py)

Autor：Kaiming He

## Background

one-stage detector 虽然在 train 和 inference 上速度快于 two-stage detector，但是 detector 的准确率较低，作者希望 one-stage detector 在保持速度优势的情况下达到 two-stage 的准确率。

## Analysis

作者认为样本**类别（包括正负样本和object class）不均衡 (class imblance during the training)** 会制约 one-stage detector 的准确率。

- two-stage detector通过对region proposal的采样来解决类别不均问题
  - Two - stage cascade
    - 仅选择高质量的 region proposal 为1000 ～ 2000。（RPN net(256 bboxes))
  - Biased minibatch sampling
    - **fixed foreground-to-background ratio**
    - **IoU balance sampling**
    - [ ] **OHEM**

- one-stage detecor
  - 本文提出新的loss function来处理样本不平衡问题（**focal loss**）

# Method

- **Robust loss**
  - Down-weighting the loss of examples with large errors (hard examples)
- **Alpha - balanced cross entropy**
  - ![picture](https://img-blog.csdn.net/20170810081651122?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxNDM4MDE2NQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
  - class = 1(forceground), alpha = a; classs = -1.(background), alpha = 1 - a
- **Focal loss**
  - Down-weighting inliers (easy examples) such that their contribution to the total loss is small even if their number is large.
  - It focuses training on a sparse set of hard example.
  - ![picture](https://img-blog.csdn.net/20170810081459842?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxNDM4MDE2NQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
  - ![picture](https://img-blog.csdn.net/20170810081542743?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxNDM4MDE2NQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
  - ![picture](https://img-blog.csdn.net/20170810081625814?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxNDM4MDE2NQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
  - ![picture](https://img-blog.csdn.net/20170810081717592?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxNDM4MDE2NQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
    - y = 1, FL = - (1 - p) <sup>y</sup> * log(p) , 若p -> 1，weight -> 0.
    - y = 0, FL = - p * log(1 - p), 若 p -> 0, weight -> 0.

# Experiment

- RetinaNet: ResNet-50-FPN and ResNet-101-FPN backbones

  - ![pciture](https://img-blog.csdn.net/20170810081838808?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxNDM4MDE2NQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

- Initialization

  - conv layers in class / box subnet
    - bias b = 0 and a Gaussian weight fill with σ = 0.01.

  - **For the final conv layer of classification subnet**
    - b = − log((1 − π)/π), where π specifies that at the start of training every anchor should be labeled as fore- ground with confidence of ∼π. We use π = .01.
    - 避免在开始训练的时候背景anchor过多使得模型不稳定
  - ![pciture](https://img-blog.csdn.net/20170810081923769?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxNDM4MDE2NQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)