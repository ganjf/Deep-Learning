## CornerNet and CenterNet

[CornerNet: Detecing Objects as Paired Keypoints](https://openaccess.thecvf.com/content_ECCV_2018/html/Hei_Law_CornerNet_Detecting_Objects_ECCV_2018_paper.html) [ECCV 2018]

[CenterNet: Keypoint Triplets for Object Detection](https://openaccess.thecvf.com/content_ICCV_2019/html/Duan_CenterNet_Keypoint_Triplets_for_Object_Detection_ICCV_2019_paper.html) [ICCV 2019]

### CornerNet

- ![](https://gitee.com/gggqq/src/raw/master/cornerNet_module.png?raw=true)

- Unormalized Gauusian (A variant of focal loss)

  - Reduce the penalty given to negative loacations within a radius of the positive location with unnormalized 2D Gaussian, which center is at the positive location. **（keypoint heatmap representation method, Gaussian Kernel)**
    $$
    y_{cij} = e^{-\frac{x^2+y^2}{2\sigma^2}} \quad ,\sigma=\frac{1}{3}
    $$
    <img src="https://gitee.com/gggqq/src/raw/master/corner_det_loss.png?raw=true" style="zoom:50%;" />

- **Corner Pooling**
  - ![](https://gitee.com/gggqq/src/raw/master/corner pooling.png?raw=true)
  - **Issue:**  The geometirc centers of objects do not necessarily convey very recognizable visual patters. (Eg, human head and human body).
  - **Solution:** Find the maximum value in its both horizontal and vertical directions and add them together.

- **Grouping Corners (Embedding)**
  - **Motivation:** To determine if a pair of the top-left corner and bottom-right corner is from the same bounding box.
  - **Solution:** Predict an embedding vector for each detected corner and them group the corners based on teh distance between embedding of top-left and bottom-right corners.
  - <img src="/Users/gan/Desktop/embedding loss.png" style="zoom:50%;" />
    - e<sub>k</sub> is the average of e<sub>tk</sub> and e<sub>bk</sub>.
- Loss (det + offset + embedding).
  - **loss for heatmap:** applyed at all pixel at heatmap.
  - **loss for offset and embedding:** applyed at the ground-truth corner location.

### CenterNet

- **Motivation, Possible reason for CornerNet perform poorly for small bounding box: ** Cannot look into the regions inside the bounding box. RoI pooling could look into the visual patterns in bounding boxes. 

- ![](https://gitee.com/gggqq/src/raw/master/centerNet_model.png?raw=true)
- Procedure to process predition box:
  - Select top-k center keypoint according to their scores
  -  Use the corresponding offsets to remap these center key points to the input image
  - Define a central region for each bounding box and check if the central region contains center keypoint. (label of bbox are same as label of cneter keypoint)
  - If a center keypoint is detected in the central region, preserve the bounding box, nade the score of bbox is the average scores of the three points; otherwise, remove the bounding box.
- Scale-aware central region in bounding box (prediction)
  - Generate a relatively large central region for a small bounding box, while a realatively small central region for a large bounding box.
  -  <img src="https://gitee.com/gggqq/src/raw/master/scale-aware-1.png?raw=true" style="zoom:50%;" /><img src="https://gitee.com/gggqq/src/raw/master/scale-aware-2.png?raw=true" style="zoom:50%;" />
- **Center pooling**
  - like top-left center pooling in CornerNet.
  - Center pooling can extract richer internal visual patterns, and larger objects contain more accessible internal visual patterns.
- **Cascade corner pooling**
  - **Issue: **Corners are often outside the objects, which lacks local apperance feature. To find maximum values on the boundary direction, which make corners sensitive to the edges.
  - **Solution: **It first looks along a boundar to find a boundary maximum value, **then looks inside along the loacation of the boundary maximum value to find an internal maximum value**, finally, add the two maximum values together.
  - <img src="https://gitee.com/gggqq/src/raw/master/center_cascade_corner_pooling.png?raw=true" style="zoom: 50%;" />
  - <img src="https://gitee.com/gggqq/src/raw/master/cascade_corner_pooling.png?raw=true" style="zoom: 50%;" />

