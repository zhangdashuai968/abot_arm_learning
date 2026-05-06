# E23 · Cartographer 多传感器融合建图

**阶段**：Phase 3
**预计耗时**：3-4 小时（含调参）
**前置**：E14 知道有 IMU + E21 闭环体感
**对应官方实验讲义**：`~/桌面/abot开源机器人/v01.01.01.0000/实验23_基于cartographer多传感器融合建图实验.md`

## 目标

跑通 Cartographer 融合 LiDAR + IMU + Odom 三源建图，对比与 E21 单激光 SLAM 的差异，并调一组参数看出图质量变化。

## 关键问题

1. Cartographer 的前端（local SLAM）和后端（global SLAM）分别用什么算法？
2. 为什么 Cartographer 在快速旋转时仍然稳？（IMU 的作用）
3. Cartographer 用 submap 是为了什么？submap 与 keyframe 区别？
4. `.lua` 配置文件里最常调的 5 个参数？
5. Cartographer 比 gmapping/karto 占用多少内存 / CPU？大场景能不能上？

## 执行步骤

### 1. 装 + 跑
```bash
sudo apt install ros-$ROS_DISTRO-cartographer-ros
# 或源码编译（厂家方案可能需要）
roslaunch cartographer_ros <abot>.launch
```

### 2. 写/改 .lua 配置
关键字段：
- `tracking_frame`（一般 base_link / imu_link）
- `published_frame`
- `provide_odom_frame`
- `use_imu_data` / `use_odometry`
- `num_subdivisions_per_laser_scan`（MID360 之类多线雷达要改）

### 3. 实地走一圈
- 与 E21 同一条路径
- rosbag 录下来
- 看 rviz 出图，与 E21 的图叠加对比

### 4. 调参对比
- 把 `use_imu_data` 关掉重跑，看抖不抖
- 把 submap 大小调小，看出图质量

## 产出

- `notes/E23-Cartographer.md`：
  - 配置文件关键字段速查
  - 与 E21 出图的对比（同一 bag）
  - 关 IMU 前后差异
- `notes/SLAM对比表.md` 完成版（gmapping vs karto vs cartographer）
- `cheatsheets/cartographer调参.md`

## 验收

- [ ] 同一 bag 在 cartographer 上的出图比 E21 的更稳
- [ ] 关键问题 1-5 都有答案
- [ ] 知道哪个参数控制"信任 IMU 多少"

## 执行记录

<!-- 每次执行追加一段 -->
