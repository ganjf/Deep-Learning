International Conference on RObotics and Automation (ICRA)

IEEE International Workshop on Intelligent Robots and Systems  (IROS)

International Conference on Advanced Robotics and Intelligent Systems (ARIS)

International Conference on Advanced Robotics and Mechatronics (ICARM)

IEEE Robotics and Automation Letters

## 专业名词

- end effector 末端执行器

## Grasp with robot

- Grasping pose detection (perception problem) （image plane） ｜｜ 6-D pose estimation
- Motion planning
- Motion execution

## Types and Availablity of Training Data

- Multi-Modal Data. (RGB, Depth, 3D Point Cloud) / **RGD Image** / **RGB-D Image**

  - Enhancement of  edges and contours.

- **6D pose estimation dataset**

  - **KTTI dataset (urban driving enviroment)**
  - **SUN-RGBD dataset (indoor enviroment, different types of RGB-D camera with varying resolutions.)**

  <img src="https://gitee.com/gggqq/src/raw/master/6D_pose_estimation.png" style="zoom:50%;" align="left" />

- **Grasp detection dataset**

  <img src="https://gitee.com/gggqq/src/raw/master/grasp_detection.png" style="zoom:50%;" align="left"  />

- **Grasp planning dataset**

  <img src="https://gitee.com/gggqq/src/raw/master/grasp_planning.png" style="zoom:50%;" align="left"/>

## Review

- Vison-based Robotic Grasping from Object Localization, Pose estimation, Grasp Detection to Motion Planning - A Review

## Grasp Detection

- **Evaluation Metrics**

> **rectangle metric**
>
> Difference between the grasp angles to be less than 30 degree.
>
> Jacquard index between the grasps to be less than 25%.
>
> **point metric**
>
> evaluate the distance between predicted grasp centre and the actual grasp centre relative to a threshold value. Don't consider the grasp angle.
>
> **Success rate or accuracy**
>
> The percentage of grasps that were able to lift, transport, and hold a desired object after shaking.
>
> **Precision**
>
> The success rate on grasps that are have an estimated robustness / threshold higher than 50%.
>
> **Robust grasp rate**
>
> The percentage of planned grasps with an estimated robustness / threshold higerh than 50%. (Caused by Cluttered)
>
> **Planning time**
>
> The time in seconds between receiving an image and returning a planned grasp.

### RGB Image

- [x] **Fully Convolutional Grasp Detection Network with Oriented Anchor Box [IROS 2018] [paper](https://arxiv.org/pdf/1803.02209.pdf)**
  - Real-time approach to predict multiple grasp pose **without classification**.
  - Matching Strategy
    - **The centers** of both oriented anchor box and the ground-truth rectangle locate in the same grid cell.
    - **The orientation difference** between oriented anchor box and ground-truth rectangle should below 90 / k.

- [x] Enhancement of real-time grasp detection by cascaded deep convolutional neural networks

- [x] Fast Convolutional Neural Network for Real-Time Robotic Grasp Detection [ICRA 2019]
  - Data Augmentation: Random crop that no part of object is outside the representation.
- [x] **ROI-based Robotic Grasp Detection for Object Overlapping Scenes [IROS 2019]**

- [x] **Object Detection Approach for Robot Grasp Detection [ICRA 2019] [paper](https://ieeexplore.ieee.org/abstract/document/8793751)**
  - **Transfer learning between object detection and robot grasp detection** 
  - **Rotated Region Proposals Network, RRPN**, **(IoU of skew RoI proposals)**, [[Arbitrary-Oriented Scene Text Detection via Rotation Proposals](https://arxiv.org/pdf/1703.01086v3.pdf)].

- [ ] 



## 6-D pose estimation

> The object pose can be represented by a 4 x 4 matrix P = [R, t; 0, 1], where R is a 3 x 3 rotation matrix and  t is a 3 x 1translation vector.
>
> **6D Pose: **不同物体所在的世界系，一般对应不同的世界系原点，一般是各自物体的重心，姿态由相机坐标系到物体的世界坐标系的旋转和平移表示.

- **Evaluation Metrics**

> - Average Distance of Model Points (ADD) for non-symmetric objects.

> - Average Closest Distance of Model Points (ADD-S) for symmetric objects.
> - ADD-S Cruve. (Area under curve $\rightarrow$ AUC)
>
> > An ambiguity-invariant pose error metric which takes care of both symmetric and non-symmetric objects into an overall evaluation. **(closest in the neighbor, e.g.  < 2cm define the neighbor)**

> - The visible surface discrepancy (VSD).

> - AP<sub>3D</sub> with 3D intersection-over-union ration, 3D IoU (3D bounding box).

- [ ] Deep-6dpose: recovering 6f object pose from a single rgb image.
- [ ] PoseCNN: A convolutional neural network for 6d object pose estimation in cluttered scenes
  - ShapeMatch- Loss function

- [ ] Deepim: Deep iterative matching for 6d pose estimation.

- [ ] Bb8: a scalable, accurate, robust to partial occlusion method for predicting the 3d poses of challenging objects without using depth
- [ ] Real-time seamless single shot 6d object pose prediction
- [ ] SSD-6D: Making RGB-based 3D detection and 6D pose estimation great again
- [ ] Recovering 6d object pose from rgb indoor image based on two-stage detection network with multi-task loss.
- [ ] Segmentation-driven 6D pose estimation
- [ ] DenseFusion: 6d object pose estimation by iterative dense fusion. [CVPR 2019] [[paper](https://openaccess.thecvf.com/content_CVPR_2019/papers/Wang_DenseFusion_6D_Object_Pose_Estimation_by_Iterative_Dense_Fusion_CVPR_2019_paper.pdf)]
- [ ] A novel visual -tactile data set for robotic manipulation.
- [ ] Pointfusion: Deep sensor fusion for 3d bounding box estimation
- [ ] A unified framework for multi-view multi-class object pose estimation.
- [ ] Frustum pointnets for 3d object detection from rgb-d data.
- [ ] Real-Time Seamless Single Shot 6D Object Pose Prediction
- [ ] Multi-view 3d object detection network for autonomous driving
- [ ] 

## Grasp Detection

- [ ] Grasp quality measures: review and performance.
- [ ] Dex-net 2.0: Deep learning to plan robust grasps with synthetic point clouds and analytic grasp metrics.
- [ ] 

- [ ] Multi-view self-supervised deep learning for 6d pose estimation in the amazon picking challenge.
- [ ] Learning synergies between pushing and grasping with self-supervised deep reinforcement learning.
- [ ] Silhonet: An RGB method for 3d object pose estimation and grasp planning.

### End-to-end Grasp Detection

> For the end-to-end grasp detection methods, the computed grasp point may not be the globally optimal one, as only part of the object is available in the image. Meanwhile, they only consider the geometric information,while other important factors like material and weight are missing.

- [ ] Deep learning for detecting robotic grasps
  - Demonstrated that a five-dimensional grasp configuration in 2-D can be projected to 3-D.

- [ ] Grasp pose detection in point cloud.
- [ ] Object discovery and grasp detection with a shared convolutional neural network.
- [ ] Supersizing self-supervision: Learning to grasp from 50k tries and 700 robot hours.
- [ ] Deep grasp: Detection and localization of grasps with deep neural networks.
  - Introduce a network composed of a grasp region proposal component and a robotic grasp detection component.
- [ ] Real-time, highly accurate robotic grasp detection using fully convolutional neural networks with high-resolution images.
- [ ] Robotic pick-and-place of novel objects in clutter with multi-affordance grasping and cross-domain image matching.
  - Presented a system capable of picking and recognizing novel objects with limited prior knowledge.
- [ ] 

- [ ] Real-World Multiobject, Multigrasp Detection
  - 1. Classification-based approach; 2. Perform well on the [multi-grasp dataset.](https://github.com/ivalab/grasp_multiObject)
  - 
- [ ] Real-Time, Highly Accurate Robotic Grasp Detection using Fully Convolutional Neural Network with Rotation Ensemble Module [ICRA 2020]
- [ ] A Novel Robotic Grasp Detection Technique by Integrating YOLO and Grasp Detection Deep Neural Networks [ARIS 2020]
- [ ] Grasp Detection Based on Faster Region CNN [ICARM]
- [ ] Lenz, I.; Lee, H.; Saxena, A. Deep Learning for Detecting Robotic Grasps. Int. J. Robot. Res. 2015, 34, 705–724.
- [ ] Kumra, S.; Kanan, C. Robotic grasp detection using deep convolutional neural networks. In Proceedings of the 2017 IEEE/RSJ International Conference on Intelligent Robots and Systems (IROS), Vancouver, BC, Canada, 24–28 September 2017; pp. 769–776. [CrossRef]

## Move Planning

- > **GraspIt: A versatile simulator for robotic grasping.**
  >
  > Supports the loading of objects and obstacles of arbitrary geometry to populate a completet simulation world. It allows a user to interactively manipulate a robot or an object and create contacts between them.
  >
  > **OpenGRASP: A toolkit for simulating grasping and dexterous manipulation**
  >
  > It provides a holistic environment that can deal with a variety of factors associated with robotic grasping.

### Reinforcement Learning

- [ ] Task-agnostic self-modeling machines.
- [ ] Robot grasping in clutter: Using a hierarchy of supervisiors for learning from demonstrations.
- [ ] Learning a visuomotor controller for real world robotic grasping using simulated depth images.
- [ ] Learning hand-eye coordination for robotic grasping with deep learning and large-scale data collection.

- [ ] Qt-opt: Scalable deep reinforcement learning for vision-based robotic manipulation.
  - Closed-loop real-world grasping.
- [ ] Robustness via retrying: Closed-loop robotic manipulation with self-supervised learning.
- [ ] Learning task-oriented grasping for tool manipulation from simulated self-supervision.

