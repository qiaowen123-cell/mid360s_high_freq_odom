# MID360s 高频 Odometry 输出

本项目用于 Livox MID360s + FAST-LIO 的实时里程计输出。系统通过 livox_ros_driver2 读取 MID360s 点云和 IMU 数据，再由 FAST-LIO 输出低频 LiDAR 校正 odom 和高频 IMU 外推 odom。

## 功能

- 适配 Livox MID360s
- 订阅 livox_ros_driver2/CustomMsg 点云
- 订阅 MID360s 内置 IMU
- 输出 FAST-LIO 校正后的 /Odometry
- 输出高频 IMU 外推 /Odom_high_freq
- 可输出 MAVROS 视觉位姿 /mavros/vision_pose/pose

/Odom_high_freq 的频率由 IMU 频率决定。如果 MID360s IMU 为 200Hz，则该话题可接近 200Hz。

## 输入话题

- /livox/lidar：MID360s 点云，类型为 livox_ros_driver2/CustomMsg
- /livox/imu：MID360s IMU，类型为 sensor_msgs/Imu

话题名可在 fastlio_ws/src/FAST_LIO/config/mid360.yaml 中修改。

## 输出话题

- /Odometry：LiDAR 校正后的低频 odom，频率接近雷达帧率
- /Odom_high_freq：IMU 外推的高频 odom，频率接近 IMU 频率
- /mavros/vision_pose/pose：给 MAVROS 使用的视觉位姿
- /cloud_registered：配准后的点云
- /path：轨迹

## 高频 odom 原理

FAST-LIO 每完成一帧 LiDAR 更新后，会刷新当前最优状态。之后每收到一帧 IMU 数据，就用 IMU 加速度和角速度对状态进行一次快速预测，并立即发布 /Odom_high_freq。

因此：

- /Odometry 负责低频校正，精度更稳定
- /Odom_high_freq 负责两帧 LiDAR 之间的高频位姿输出
- 高频输出是否能到 200Hz，主要取决于 /livox/imu 是否稳定到 200Hz

## Acknowledgement

本项目基于 FAST-LIO、livox_ros_driver2 和 HNU-CAT/high_fast_lio2 修改，请遵守原项目开源协议。

感谢以下开源项目：

- FAST-LIO
- livox_ros_driver2
- HNU-CAT/high_fast_lio2：https://github.com/HNU-CAT/high_fast_lio2
