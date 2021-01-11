## ExtremeNet

[Bottom-up Object Detection by Grouping Extreme and Center Points](https://arxiv.org/abs/1901.08043)

[Github]( https://github.com/xingyizhou/ ExtremeNet)

- **Difference between Top-down and Bottom-up detector.** (Eg. There are two person in the picture, all human has 15 joint (keypoints))
  - Top-down approach
    - find two bounding boxes including each person.
    - estimate human joint (15 keypoints) per each bounding box.
  - Bottom-up approach
    - estimate all human joint (30 keypoints) in the picture.
    - Classify which joint (15 keypoints) are included in the same person.
  - In general situation, Top-down approach consume time much more than Bottom-up, because Top-down approach need N-times pose estimation by person detector results. （N - object nums）

- Model: <img src="https://img-blog.csdnimg.cn/20190131163717828.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzM3NTMyMDY1,size_16,color_FFFFFF,t_70" style="zoom:50%;" />



- ExtractPeak (same as CornerNet and CenterNet)

  - A peak is any pixel location with a value greater than threshold, that is locally maximal in a 3 x 3 window surrounding the pixel.

- **Center Grouping**

  - Iterate all possible combinations among four extreme points. (top, bottom, right, left)
  - ![](https://img-blog.csdnimg.cn/2019012921201649.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzM3NTMyMDY1,size_16,color_FFFFFF,t_70)

- **Ghost box suppression**

  - <img src="https://baiyfbupt.github.io/img/post/ghostbox.jpg" style="zoom: 50%;" />
  - For three equally spaced collinear objects of the same size, the center object could be the <u>correct</u> small box or a <u>wrong</u> much larger box containing the extreme points of its neighbor.

  - **Soft NMS**: If the sum of scores of all boxes contained in a certain bounding box exceeds 3 times of the score of itself, divided its score by 2.

- **Edge aggregation**
  - **Issue：**network produces a weak response along any aligned edges of the object, instea of a single strong peak response, so that the peak may be missed.
  - **Aggregation: ** aggregate all monotonically decrease scores with weighted factor, and stop the aggregation at a local minmum along the aggregation direction. <u>(vertical direction for left and. right extreme points and horizontal direction for top and bottom keypoints.)</u>
  - ![](https://img-blog.csdnimg.cn/20190131164107206.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzM3NTMyMDY1,size_16,color_FFFFFF,t_70)