## Cascade R-CNN: Delving into High Quality Object Detection

Paper: https://openaccess.thecvf.com/content_cvpr_2018/papers/Cai_Cascade_R-CNN_Delving_CVPR_2018_paper.pdf [CVPR 2018]

GitHub: *https://github.com/zhaoweicai/cascade-rcnn* [mmdetection]

- **Contribution**
  - Propose Cascade R-CNN, for the design of high quality object detectors. The architecture is to avoid the problems of overfitting (postive RoI sampling ) at training and quality mismatch at inference. 

- **Issues**
  - Quality match between hypotheses (region proposal.) and detector. 
    - <img src="https://pic4.zhimg.com/80/v2-48e61ccdccfe1b422d25271a20428c7b_1440w.jpg" alt="v2-48e61ccdccfe1b422d25271a20428c7b_1440w" style="zoom:50%;" />
    - Train the three detectors with IoU thresholds of u = 0.5, 0.6, 0.7.
    - **Fig c suggests**, each bounding box regressor performs best for examples of IoU close to the threshold that the detector was trained.  <u>(for a fixed input RPN IoU, train with the  closed IoU could perform better</u>.)
    - **Fig c,d suggests,**  the higher quality detection requires a closer quality match between the detector  and the hypotheses  that it processes.
    - **Fig c suggests,** the output of a detector trained with a certain IoU threshold is a good distribution to train the detector of the next higher IoU threshold.
  - Mismatch issue between training and inferenece.
    - when inferencing, detector will detect and regress with all region proposal (not sampling), the IoU distribution of proposal may have a large difference with training process(sampling).  （high quality of detector are only optimal for high quality of hypotheses).
- Cascade R-CNN
  - ![](https://gitee.com/gggqq/src/raw/master/Cascade%20R-CNN.png?raw=true)
  - Resampling mechanism to improve the quality of proposals. Where the distribution tilts more heavily towards high quality examples after each resampling step.
    - <u>**There is no overfitting, since positive examples are plentiful at all levels.**</u> (regressor could generate more realiable proposals with high quality.)
    - The detector of detectors of the deeper stages are optimized for higher IoU thresholds.
    - **At inference, the quality of the hypotheses is sequentially improved, and higer quality detector are only required to operate on higher quality hypotheses.**

