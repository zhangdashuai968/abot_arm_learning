# small-car ↔ 实验映射表

> 最后更新：2026-05-26
> 作用：在 `abot_arm_learning` 学完某个实验后，到 `small-car` 仓库找到对应源码落地验证。

---

## Phase 2 · 底盘 + 运动学

| 实验 | 实验内容 | small-car 对应路径 |
|------|----------|-------------------|
| E10 | ROS 控制底盘移动 | `src/abot_project/abot/launch/bringup.launch` — 底盘驱动 + 里程计 + IMU |
| E11 | 运动学正解 | —（机械臂运动学，small-car 尚无机械臂 URDF） |
| E12 | 运动学逆解 | —（同上） |
| E13 | 本体结构建模 | —（URDF 模型在车上 `~/catkin_ws/src/abot_project/abot/urdf/`） |
| E14 | 避障传感器数据读取 | `src/abot_project/abot/launch/lidar/rplidar.launch` — RPLidar C1 驱动 |

---

## Phase 3 · SLAM + 建图导航

| 实验 | 实验内容 | small-car 对应路径 |
|------|----------|-------------------|
| E21 | 激光 SLAM 闭环检测 | `src/abot_project/abot/launch/slam/karto_slam.launch` — karto SLAM 闭环检测 |
| | | `src/abot_project/abot/launch/slam/hector_slam.launch` — hector 无里程计建图 |
| E22 | G2O 图优化 | `src/abot_project/abot/launch/slam/cartographer_slam.launch` — 后端图优化（Cartographer 内置） |
| E23 | Cartographer 融合建图 | `src/abot_project/abot/launch/slam/cartographer_slam.launch` — LiDAR+IMU+Odom 三源融合 |
| | | `src/abot_project/abot/launch/slam/cartographer_3d.launch` — 3D 建图 |
| E24 | 导航与避障 | `src/abot_project/abot/launch/navigate.launch` — move_base + AMCL 总入口 |
| | | `src/abot_project/abot/param/navigation/teb/teb_omni_planner_params.yaml` — TEB 规划器参数 |
| | | `src/abot_project/abot/param/navigation/dwa/dwa_omni_planner_params.yaml` — DWA 规划器参数 |
| | | `src/abot_project/abot/param/navigation/common/move_base_params.yaml` — move_base 全局参数 |
| | | `src/abot_project/abot/param/navigation/common/costmap_x4_params.yaml` — costmap 参数 |
| | | `src/abot_project/abot/launch/move_base.launch` — move_base 独立启动 |
| | | `src/abot_project/abot/launch/amcl.launch` — AMCL 定位 |
| | | `src/abot_project/abot/param/lidar/x4_laserfilter.yaml` — 激光滤波（遮车体） |

---

## Phase 4 · 视觉感知 + 抓取

| 实验 | 实验内容 | small-car 对应路径 |
|------|----------|-------------------|
| E27 | 相机数据采集 | `src/abot_project/abot/launch/camera.launch` — Astra RGBD 相机启动 |
| | | `src/abot_project/abot/launch/depth_camera/` — 深度相机 launch 文件集 |
| E28 | 颜色识别 | —（OpenCV 节点，待部署到 small-car） |
| E31 | 实时目标检测 | —（YOLO ROS 节点，待部署到 small-car） |
| E32 | ROS 视觉伺服通信 | —（检测→3D→IK→抓取闭环，待开发） |

---

## 全局映射

| 知识模块 | small-car 路径 | 说明 |
|----------|---------------|------|
| 激光雷达 | `src/abot_project/abot/launch/lidar/rplidar.launch` | RPLidar C1 460800 bps |
| | `src/abot_project/abot/param/lidar/x4_laserfilter.yaml` | 车体遮挡滤波 |
| SLAM | `src/abot_project/abot/launch/slam/` | 多种建图方案（Cartographer/Hector/Karto） |
| 导航 | `src/abot_project/abot/launch/navigate.launch` | 总入口 |
| | `src/abot_project/abot/param/navigation/` | 规划器 + costmap 参数 |
| 定位 | `src/abot_project/abot/launch/amcl.launch` | AMCL 自适应蒙特卡洛定位 |
| 底盘 | `src/abot_project/abot/launch/bringup.launch` | 底盘 + 里程计 + EKF |
| 地图 | `src/abot_project/abot/maps/` | house.yaml / my1_map.yaml |
| 测试脚本 | `src/abot_project/abot/script/abot_patrol_nav.py` | 巡逻导航脚本 |
| | `../scripts/nav_test.py` | 前进 1m 测试 |
| | `../scripts/rotate_test.py` | 旋转 90° 测试 |

---

## 使用方式

学完某个实验后：
1. 对照此表找到 small-car 对应路径
2. 阅读源码，理解参数含义
3. 在真机上改一个参数跑一遍，观察变化
4. 将发现记入 `logs/` 对应日志

## 修订记录

| 日期 | 变更 |
|------|------|
| 2026-05-26 | 初版 |
