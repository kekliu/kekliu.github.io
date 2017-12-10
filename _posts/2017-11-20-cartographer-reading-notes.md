---
layout:     post
title:      "UML in Cartographer"
subtitle:   ""
date:       2017-11-20 09:16:17 +0800
author:     "kekliu"
header-img: "img/post-bg-unix-linux.jpg"
catalog:    true
tags:
    - SLAM
    - cartographer
---

## What is Cartographer?
[Cartographer][cartographer] is a system that provides real-time simultaneous localization and mapping (SLAM) in 2D and 3D across multiple platforms and sensor configurations. [Cartographer ROS][cartographer] is an Cartographer's ROS integration.


## Cartographer源码阅读笔记
### 1. 程序总体框架和流程说明(含cartographer-ros)
#### 1.1 总体UML
![fjl]({{"/img/slam/cartographer-execution-flow.png"}})
Note: Two key classes: MapBuilder for building map, SensorBridge for preprocessing and sending sensor data to top level.
##### 1.2 关键类说明
* OrderedMultiQueue 储存传感器数据
* CeresScanMatcher 提供扫描得到的点云到概率格网的匹配算法
* OptimazationProblem 实现了SPA(Sparse Pose Adjustment)算法,用于闭环优化



[cartographer]: https://github.com/googlecartographer/cartographer
[cartographer-ros]: https://github.com/googlecartographer/cartographer_ros
