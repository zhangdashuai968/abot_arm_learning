# 速查 · launch 文件索引

> 最后更新：2026-05-26
> 来源：small-car 真机 `src/abot_project/abot/launch/`

---

## 核心 launch（按启动顺序）

| Launch 文件 | 用途 | 启动方式 |
|------------|------|----------|
| `bringup.launch` | 底盘驱动 + 里程计 + IMU + EKF + laser + camera | `roslaunch abot bringup.launch` |
| `navigate.launch` | move_base(TEB) + AMCL + map_server + laser filter | `roslaunch abot navigate.launch` |
| `amcl.launch` | AMCL 自适应蒙特卡洛定位 | `roslaunch abot amcl.launch` |
| `move_base.launch` | move_base 独立启动（不含 AMCL） | `roslaunch abot move_base.launch` |

---

## SLAM 建图（Phase 3）

| Launch 文件 | 用途 | 适用场景 |
|------------|------|----------|
| `slam/cartographer_slam.launch` | Cartographer 2D（LiDAR+IMU+Odom） | **首选**，精度最高 |
| `slam/cartographer_3d.launch` | Cartographer 3D | 需要高度信息的场景 |
| `slam/cartographer_3dto2d.launch` | Cartographer 3D→2D 转换 | 3D 数据输出 2D 地图 |
| `slam/karto_slam.launch` | Karto SLAM（含闭环检测） | 理解闭环检测原理 |
| `slam/hector_slam.launch` | Hector SLAM（无里程计） | IMU 挂了时的备选 |
| `slam/lidar_slam.launch` | 通用激光 SLAM 入口 | 快速测试 |
| `slam/auto_slam.launch` | 自动建图（含自动保存） | 一键建图 |
| `slam/rrt_slam.launch` | RRT SLAM | 实验性方案 |
| `auto_map_save.launch` | 自动保存地图 | 建图结束后调用 |
| `laser_scan_odom.launch` | 激光里程计 | RF2O 激光里程计 |

---

## 传感器

| Launch 文件 | 用途 | 关键参数 |
|------------|------|----------|
| `lidar/rplidar.launch` | RPLidar C1 驱动 | `serial_baudrate=460800` |
| `lidar/lslidar.launch` | 乐扫 N10 驱动 | 串口直连 |
| `lidar/rslidar.launch` | 速腾激光驱动 | 备用 |
| `camera.launch` | Astra RGBD 深度相机 | USB 3.0 |
| `depth_camera/depth_camera_laser.launch` | 深度相机→伪激光 | 深度图转 LaserScan |
| `depth_camera/depth_camera_gmapping.launch` | 深度相机 + Gmapping | 激光坏了时的备选 |
| `depth_camera/depth_camera_navigate.launch` | 深度相机 + 导航 | 纯视觉导航 |

---

## 导航参数（`param/navigation/`）

| 文件 | 用途 |
|------|------|
| `common/move_base_params.yaml` | move_base 全局参数（controller_patience=5） |
| `common/costmap_x4_params.yaml` | x4 底盘 costmap 参数 |
| `common/global_costmap_params.yaml` | 全局 costmap 参数 |
| `common/local_costmap_params.yaml` | 本地 costmap 参数 |
| `teb/teb_omni_planner_params.yaml` | TEB 全向规划器参数 |
| `dwa/dwa_omni_planner_params.yaml` | DWA 全向规划器参数 |

---

## 激光滤波（`param/lidar/`）

| 文件 | 用途 |
|------|------|
| `x4_laserfilter.yaml` | x4 底盘激光滤波（min_x=-0.25 遮车体） |

---

## 启动链路（一条命令版）

```bash
# 完整导航启动
roslaunch abot bringup.launch && roslaunch abot navigate.launch

# 仅建图
roslaunch abot bringup.launch && roslaunch abot slam/cartographer_slam.launch

# 仅底盘（测试用）
roslaunch abot bringup.launch

# 一键建图（建完自动保存）
bash scripts/start_map.sh
```

---

## 修改记录

| 日期 | 变更 |
|------|------|
| 2026-05-26 | 初版 |
