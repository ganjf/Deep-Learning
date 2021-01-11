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

- Multi-Modal Data. (RGB, Depth, 3D Point Cloud) / **RGD Image** / **RGBD Image**
  - Enhancement of  edges and contours.
- The Cornell Grasp Dataset (CGD): 

## RGB IMAGE

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



## RGB-D IMAGE

- [ ] Real-World Multiobject, Multigrasp Detection
  - 1. Classification-based approach; 2. Perform well on the [multi-grasp dataset.](https://github.com/ivalab/grasp_multiObject)
  - 
- [ ] Real-Time, Highly Accurate Robotic Grasp Detection using Fully Convolutional Neural Network with Rotation Ensemble Module [ICRA 2020]
- [ ] A Novel Robotic Grasp Detection Technique by Integrating YOLO and Grasp Detection Deep Neural Networks [ARIS 2020]
- [ ] Grasp Detection Based on Faster Region CNN [ICARM]
- [ ] Deep learning for detecting robotic grasps
  - Demonstrated that a five-dimensional grasp configuration in 2-D can be projected to 3-D. 

