# abot 机械臂车系统架构

> 软件栈全景：ROS 节点拓扑、TF 树、数据流、坐标系约定
> 配合 [../cheatsheets/启动链路.md](../cheatsheets/启动链路.md) 和 [../cheatsheets/TF-tree.md](../cheatsheets/TF-tree.md) 阅读

---

## 1. 硬件拓扑

```
┌─────────────────────────────────────────────────────┐
│                    abot M1 ARM 整车                    │
│                                                       │
│  ┌──────────────┐  ┌──────────────┐                  │
│  │  Orbbec Astra │  │ RPLIDAR A3M12│                  │
│  │  深度相机      │  │  360° 激光雷达 │                  │
│  │  (USB 3.0)    │  │  (USB 转串口)  │                  │
│  └──────┬───────┘  └──────┬───────┘                  │
│         │                 │                           │
│  ┌──────┴─────────────────┴───────┐                  │
│  │       NVIDIA Jetson Nano        │                  │
│  │       Ubuntu 18.04 + ROS Melodic│                  │
│  │       ROS Master                │                  │
│  └──────┬─────────────────┬───────┘                  │
│         │                 │                           │
│  ┌──────┴───────┐  ┌──────┴───────┐                  │
│  │  Zeus S2     │  │  ROCR6       │                  │
│  │  麦克纳姆轮底盘│  │  六轴协作机械臂 │                  │
│  │  (/dev/ttyACM0)│  │  + G3P 夹爪   │                  │
│  └──────────────┘  └──────────────┘                  │
│                                                       │
│  ┌──────────────┐                                    │
│  │  MPU6050 IMU │  ← 底盘中心                         │
│  │  (USB 转串口)  │                                    │
│  └──────────────┘                                    │
└─────────────────────────────────────────────────────┘
```

### 硬件清单

| 组件 | 型号 | 接口 | 设备节点 |
|------|------|------|----------|
| 主控 | NVIDIA Jetson (Tegra) | — | — |
| 底盘 | Zeus S2 重载麦克纳姆轮 | USB 转串口 | `/dev/ttyACM0` |
| 机械臂 | ROCR6 六轴协作 | USB + Ethernet | — |
| 夹爪 | G3P 柔性二指 | 机械臂末端 IO | — |
| 激光雷达 | RPLidar A3M12 | USB 转串口 | `/dev/ttyUSB0` 或 `1` |
| 深度相机 | Orbbec Astra | USB 3.0 | — |
| IMU | MPU6050 (N100) | USB 转串口 | `/dev/ttyUSB0` 或 `1` |

---

## 2. TF 坐标变换树

```
map                          ← AMCL 发布（map → odom 修正）
 │
 odom                         ← EKF 融合发布（odom → base_footprint）
 │
 base_footprint               ← 底盘投影到地面
 │
 base_link                    ← 底盘本体坐标系（原点在底盘中心）
 │
 ├── laser                    ← RPLidar C1
 ├── camera_link              ← Astra RGBD 深度相机
 │   ├── camera_rgb_frame
 │   ├── camera_depth_frame
 │   └── camera_depth_optical_frame
 ├── imu_link                 ← MPU6050
 │
 └── [机械臂链]
     └── joint1 → joint2 → joint3 → joint4 → joint5 → joint6
         └── gripper (G3P 夹爪)
```

> 完整 tf 树含调试命令见 [../cheatsheets/TF-tree.md](../cheatsheets/TF-tree.md)

---

## 3. ROS 节点拓扑图

### 按 Phase 分层的节点交互

```
┌─ Phase 1: 认知层 ──────────────────────────────────┐
│  roscore (Master URI: http://192.168.43.211:11311) │
└────────────────────────────────────────────────────┘
                         │
┌─ Phase 2: 驱动层 (bringup) ───────────────────────┐
│  rplidar_ros     → /scan                           │
│  astra_camera    → /camera/rgb, /camera/depth       │
│  imu_driver      → /imu/data                        │
│  zeus_driver     → /raw_odom (轮速里程计)            │
│  robot_state_publisher → /tf (URDF → 静态 tf)       │
└────────────────────────────────────────────────────┘
                         │
┌─ Phase 3: 定位与建图层 ────────────────────────────┐
│  robot_localization (EKF) → /odom (融合轮速+IMU)    │
│  gmapping / cartographer → /map (SLAM 建图)         │
│  AMCL → map→odom 修正 (定位模式)                     │
└────────────────────────────────────────────────────┘
                         │
┌─ Phase 4: 导航与运动层 ────────────────────────────┐
│  move_base / 自研原语  → /cmd_vel                    │
│  运动脚本 (ten_point_race 等)                        │
└────────────────────────────────────────────────────┘
                         │
┌─ Phase 4: 视觉感知层 ──────────────────────────────┐
│  YOLOv5/abot_yolo_detection → 目标检测              │
│  vl_locate (VLM 视觉定位)                            │
│  abot_object_detect → 物体位姿估计                   │
└────────────────────────────────────────────────────┘
                         │
┌─ Phase 4: 抓取伺服层 ──────────────────────────────┐
│  abot_xarm (ROCR6 driver)                            │
│  ZachLab_grasp → 抓取动作编排                        │
│  abot_tracker_servo → 视觉伺服跟踪                   │
└────────────────────────────────────────────────────┘
```

---

## 4. 数据流图

```
传感器                          处理                          控制
───────                        ──────                        ──────

轮速编码器  ──→ /raw_odom ──┐
                              ├──→ EKF ──→ /odom ──→ AMCL ──→ /tf (map→odom)
IMU (MPU6050) ──→ /imu/data ─┘

RPLidar    ──→ /scan ──→ gmapping/cartographer ──→ /map
                                        │
                                        └──→ move_base/自研原语 ──→ /cmd_vel ──→ 底盘

Astra RGBD ──→ /camera/rgb ──→ YOLO ──→ 检测框
            ──→ /camera/depth ──→ 深度滤波 ──→ 3D 坐标 ──→ 抓取规划 ──→ 机械臂

ROCR6      ←── /joint_states (反馈)
           ──→ /joint_cmds (控制)
```

---

## 5. 坐标系约定

| 坐标系 | 发布者 | 含义 | 原点 |
|--------|--------|------|------|
| `map` | AMCL | 全局地图坐标系 | 地图文件定义的 origin |
| `odom` | EKF | 里程计累计坐标系 | 开机位置 |
| `base_footprint` | URDF 投影 | 底盘地面投影 | 底盘中心 z=0 |
| `base_link` | URDF | 底盘本体 | 底盘几何中心 |
| `laser` | rplidar driver | 激光雷达 | 雷达安装位 |
| `imu_link` | IMU driver | 惯性测量单元 | 底盘中心 |
| `camera_link` | URDF | 深度相机 | 龙门架安装位 |
| `tool0` | ROCR6 URDF | 机械臂末端法兰 | 法兰中心 |

### 坐标系层级关系

```
map → odom → base_footprint → base_link → [sensor links]
                                       → joint1 → ... → joint6 → tool0 → gripper
```

---

## 6. 启动链路

参考 [../cheatsheets/启动链路.md](../cheatsheets/启动链路.md)

### 最简启动

```bash
source ~/.bashrc
cd ~/catkin_ws
catkin_make
source devel/setup.bash
roslaunch abot bringup.launch
```

### 完整导航

```bash
roslaunch abot bringup.launch
roslaunch abot navigate.launch
```

### 仅建图

```bash
roslaunch abot bringup.launch
roslaunch abot slam/cartographer_slam.launch
```

---

## 7. 导航方案说明

当前 small-car 真机采用**绕开 move_base/TEB/DWA** 的自研方案：

- **控制原语**：锁航向、只沿 X/Y 轴平移 + 原地旋转
- **全局规划**：A* 4 连通 + 障碍膨胀
- **定位**：EKF 融合轮速+IMU，航向 100% 来自 IMU
- **建图与定位互斥**：gmapping/AMCL 都发 map→odom，不能同时跑

> 详细设计见 small-car 仓库 `docs/ARCHITECTURE.md` 和 `logs/` 决策日志

---

## 8. 关联资源

| 资源 | 路径 |
|------|------|
| TF 树速查 | [../cheatsheets/TF-tree.md](../cheatsheets/TF-tree.md) |
| 启动链路 | [../cheatsheets/启动链路.md](../cheatsheets/启动链路.md) |
| Launch 索引 | [../cheatsheets/launch索引.md](../cheatsheets/launch索引.md) |
| 实验→源码映射 | [../cheatsheets/small-car-实验映射表.md](../cheatsheets/small-car-实验映射表.md) |
| 术语表 | [glossary.md](glossary.md) |
| 真机架构 | [small-car docs/ARCHITECTURE.md](https://github.com/zhangdashuai968/small-car/blob/main/docs/ARCHITECTURE.md) |
