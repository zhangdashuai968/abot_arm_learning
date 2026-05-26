# 速查 · TF 坐标变换树

> 最后更新：2026-05-26
> 来源：small-car 真机 rqt_tf_tree + bringup.launch 中的 tf 发布关系

---

## TF 树全景图

```
map                          ← AMCL 发布（map → odom 修正）
 │
 odom                         ← EKF 融合发布（odom → base_footprint）
 │
 base_footprint               ← 底盘投影到地面
 │
 base_link                    ← 底盘本体坐标系（原点在底盘中心）
 │
 ├── laser                    ← RPLidar C1（x4 加 3.14 rad 旋转）
 │
 ├── camera_link              ← Astra RGBD 深度相机（在龙门架上）
 │   ├── camera_rgb_frame     ← RGB 图像坐标系
 │   ├── camera_depth_frame   ← 深度图像坐标系
 │   └── camera_depth_optical_frame  ← 深度光心坐标系
 │
 ├── imu_link                 ← MPU6050（N100，底盘中心）
 │
 └── base_footprint_left      ← 左轮投影（里程计用）

[机械臂链 — 暂无 small-car 真机，以下为 ROCR6 标准链]

 base_link
  └── joint1 (基座旋转)
      └── joint2 (肩部)
          └── joint3 (肘部)
              └── joint4 (腕部1)
                  └── joint5 (腕部2)
                      └── joint6 (末端)
                          └── gripper (G3P 夹爪)
```

---

## 关键坐标系的含义

| 坐标系 | 发布者 | 含义 |
|--------|--------|------|
| `map` | AMCL | 全局地图坐标系，原点在地图文件定义的 origin |
| `odom` | EKF (`robot_localization`) | 里程计累计坐标系，开机后连续变化 |
| `base_footprint` | `base_link` → ground projection | 底盘在地面的投影，z=0 |
| `base_link` | URDF / robot_state_publisher | 底盘本体，原点在底盘几何中心 |
| `laser` | rplidar driver | 激光雷达坐标系，x 轴朝前（车头） |
| `imu_link` | IMU driver | 惯性测量单元，x 朝前 z 朝上 |

---

## 常见问题排查

### rviz 一片空白

1. Fixed Frame 不对 — 检查是否选 `map` 或 `odom`
2. tf 断了 — `rosrun rqt_tf_tree rqt_tf_tree` 看链条
3. 发布者没启动 — `rostopic hz /tf` 检查发布率

### 地图里机器人位置不对

1. AMCL 初始位姿不匹配 — 用 `2D Pose Estimate` 重设
2. EKF 发散 — 检查 IMU 和里程计数据是否正常

### 点云歪到天上去

1. `laser` → `base_link` 的 tf 旋转/平移写错了
2. rplidar launch 中的 `tf_rotation` 参数不正确

---

## 常用调试命令

```bash
# 看当前 tf 树
rosrun rqt_tf_tree rqt_tf_tree

# 看 map → base_link 变换
rosrun tf tf_echo map base_link

# 看 tf 发布率
rostopic hz /tf

# 导出 PDF 版 tf 树
rosrun tf view_frames
```

---

## 修改记录

| 日期 | 变更 |
|------|------|
| 2026-05-26 | 初版 |
