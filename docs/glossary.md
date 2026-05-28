# 术语表

> 按领域分组。每条术语含一句话解释 + 首次出现的实验编号 + 类比（如适用）。
> 配合 [ARCHITECTURE.md](ARCHITECTURE.md) 阅读。

---

## ROS 核心

| 术语 | 解释 | 首次出现 | 类比 |
|------|------|---------|------|
| ROS Master (roscore) | ROS 网络的中央注册表，负责节点发现和撮合 | E03 | 通讯录/电话总机 |
| Node | ROS 中的进程单元，每个可执行文件是一个节点 | E03 | 独立程序 |
| Topic | 命名的数据总线，节点通过它异步收发消息 | E03 | 广播电台 |
| Message | Topic 上传输的数据结构（.msg 文件定义） | E04 | 信封格式 |
| Publisher | 向 Topic 发送消息的节点 | E03 | 电台发射机 |
| Subscriber | 从 Topic 接收消息的节点 | E03 | 收音机 |
| Service | 同步的请求-响应通信机制 | E05 | 打电话 |
| Action | 带反馈的长时间任务通信机制 | E06 | 外卖下单（下单→接单→配送→完成） |
| rosparam | ROS 参数服务器，存储全局配置 | E04 | 全局变量表 |
| bag | ROS 数据记录格式，可回放传感器数据 | E09 | 行车记录仪 |
| launch 文件 | XML 格式的启动配置，一次启动多个节点 | E03 | 一键启动脚本 |
| catkin | ROS1 的构建系统 | E03 | CMake + package.xml |
| catkin_make | 编译工作空间所有包的命令 | E03 | make |

---

## 机器人学

| 术语 | 解释 | 首次出现 |
|------|------|---------|
| DH 参数 | Denavit-Hartenberg 参数，用 4 个值描述机械臂连杆关系 | E11 |
| 正向运动学 (FK) | 给定关节角，计算末端位姿 | E11 |
| 逆向运动学 (IK) | 给定末端位姿，反算关节角（可能有多解或无解） | E12 |
| 奇异点 (Singularity) | 机械臂失去某个方向自由度的特殊位姿 | E12 |
| URDF | Unified Robot Description Format，机器人模型描述文件 | E13 |
| TF | ROS 的坐标变换系统，维护各坐标系间的关系 | E02 |
| 麦克纳姆轮 (Mecanum) | 可实现全向移动的特殊轮组，Zeus S2 底盘使用 | E01 |
| 六轴机械臂 | 6 个旋转关节的串联机械臂，ROCR6 为此类型 | E01 |
| 末端执行器 (End Effector) | 机械臂末端工具，本车为 G3P 二指夹爪 | E01 |

---

## SLAM 与导航

| 术语 | 解释 | 首次出现 |
|------|------|---------|
| SLAM | Simultaneous Localization And Mapping，同时定位与建图 | E21 |
| 闭环检测 (Loop Closure) | 识别回到已知位置，修正累积漂移 | E21 |
| AMCL | Adaptive Monte Carlo Localization，粒子滤波自适应定位 | E24 |
| 粒子滤波 | 用一堆加权"猜测点"表示机器人位姿概率分布 | E24 |
| 占据栅格地图 (Occupancy Grid) | 将空间离散为网格，每个格子标记占用/空闲/未知 | E17 |
| Cartographer | Google 的多传感器融合 2D/3D SLAM 方案 | E23 |
| Gmapping | 基于粒子滤波的经典 2D 激光 SLAM 方案 | E21 |
| G2O | General Graph Optimization，图优化框架 | E22 |
| EKF | Extended Kalman Filter，扩展卡尔曼滤波，用于多传感器融合 | E02 |
| 里程计 (Odometry) | 通过轮速/IMU 推算的运动估计 | E10 |
| move_base | ROS 导航栈核心节点，整合全局规划+局部规划+恢复行为 | E24 |
| TEB | Timed Elastic Band，局部路径规划器，优化轨迹时间+避障 | E24 |
| DWA | Dynamic Window Approach，局部路径规划器，在速度空间采样 | E24 |
| costmap | 代价地图，将障碍物/未知区域/膨胀区编码为通行代价 | E24 |

---

## 视觉感知

| 术语 | 解释 | 首次出现 |
|------|------|---------|
| 内参标定 | 确定相机焦距、光心、畸变系数 | E25 |
| 外参标定 | 确定相机在世界坐标系中的位姿 | E25 |
| 手眼标定 | 确定相机相对于机械臂末端的位姿关系（Eye-in-Hand / Eye-to-Hand） | E26 |
| RGBD 相机 | 同时输出彩色图像和深度图像的相机 | E02 |
| HSV 颜色空间 | 色调-饱和度-明度，比 RGB 更适合颜色识别 | E28 |
| YOLO | You Only Look Once，单阶段实时目标检测算法 | E30 |
| YOLOv5/v8 | YOLO 的 PyTorch 实现版本 | E30 |
| 视觉伺服 (Visual Servoing) | 用视觉反馈闭环控制机械臂运动 | E32 |
| 数据集 | 标注好的图像集合，用于训练检测模型 | E29 |
| TensorRT | NVIDIA 推理加速引擎，优化模型在 Jetson 上的运行 | E31 |
| VLM | Vision-Language Model，视觉语言模型，用于语义理解定位 | — |

---

## 硬件

| 术语 | 解释 |
|------|------|
| Zeus S2 | 中科深谷重载麦克纳姆轮底盘，载重 50kg |
| ROCR6 | 中科深谷六轴协作机械臂，负载 3kg |
| G3P | 柔性二指夹持器，自适应包络抓取 |
| Orbbec Astra | 奥比中光深度相机，RGBD 传感器 |
| RPLIDAR A3M12 | 思岚 360° 激光雷达，测距 25m |
| MPU6050 | 6 轴惯性测量单元（3 轴加速度+3 轴陀螺仪） |
| Jetson Nano | NVIDIA 嵌入式计算平台（Tegra X1） |
| udev | Linux 设备管理器，可设固定设备名 |

---

## 缩写速查

| 缩写 | 全称 |
|------|------|
| ROS | Robot Operating System |
| SLAM | Simultaneous Localization And Mapping |
| AMCL | Adaptive Monte Carlo Localization |
| EKF | Extended Kalman Filter |
| URDF | Unified Robot Description Format |
| TF | Transform (ROS 坐标变换) |
| DH | Denavit-Hartenberg |
| FK / IK | Forward / Inverse Kinematics |
| TEB | Timed Elastic Band |
| DWA | Dynamic Window Approach |
| YOLO | You Only Look Once |
| VLM | Vision-Language Model |
| IMU | Inertial Measurement Unit |
| RGBD | Red Green Blue + Depth |
