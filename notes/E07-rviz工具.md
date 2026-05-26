# E07 · ROS 中 rviz 工具使用实验

**来源**：Jason O'Kane《A Gentle Introduction to ROS》Chapter 8（tf 与 rviz）+ 官方实验讲义
**阶段**：Phase 1
**核心概念**：rviz、Display 类型、Fixed Frame、tf 可视化、Marker

---

## 关键问题（执行前先尝试用直觉回答）

1. rviz 里的 Fixed Frame 是什么？为什么选错 Fixed Frame 后 rviz 可能一片空白？
2. 你有一个激光雷达装在 `base_laser_link` 上、机器人本体在 `base_link`。如果 Fixed Frame 选 `base_laser_link`，rviz 里看到的世界是怎样的？选 `map` 又是怎样的？
3. rviz 中添加 LaserScan Display 后看不到点云，可能是什么原因？（至少列出 3 种排查方向）
4. Marker 和普通的几何可视化（比如画一条线）有什么本质区别？为什么说 "When in doubt, publish a Marker"？
5. rviz 的配置文件是什么格式？它保存了什么信息？团队协作中怎么复用？

---

## 这节解决什么问题

ROS 中有大量感知数据（点云、图像、激光扫描、机器人模型）和抽象信息（路径、坐标变换）。你需要一个工具把它们**叠在一起可视化**，而不是对着终端里的一串数字想象。这就是 rviz。

---

## 启动 rviz

```bash
# 方法 1：单独启动
rosrun rviz rviz

# 方法 2：在 launch 文件中直接调
<node name="rviz" pkg="rviz" type="rviz" args="-d $(find my_pkg)/rviz/config.rviz" />
```

> 用 `-d` 指定配置文件可以保存和复现显示布局。

---

## Fixed Frame 概念（O'Kane 的核心讲解）

rviz 中最关键、最容易被新手忽略的配置就是 **Fixed Frame**（左侧 Displays 面板 → Global Options → Fixed Frame）。

```
场景：你有激光雷达在 "base_laser_link" 上
      你有里程计在 "odom" 下

如果 Fixed Frame 选：
  - "base_laser_link" → 激光点云显示在正确位置，但地图会围着激光转
  - "odom" → 地图静止，激光点云随机器人移动
  - "map" → 全局地图固定，机器人在地图中移动
```

**选择原则**：
- 你想看"机器人看到了什么" → 选 `base_link`
- 你想看"机器人在哪里" → 选 `map` 或 `odom`
- Fixed Frame 必须正在被发布（有 tf 数据），否则 rviz 会报错并一片空白

> O'Kane 反复强调：**如果 rviz 里什么都看不到，90% 的原因是 Fixed Frame 没选对或该 frame 没有 tf 数据可用。**

---

## rviz 主要 Display 类型

### 1. TF — 可视化坐标系变换

```
Add → By display type → TF
```

勾选后，rviz 会用彩色箭头（RGB 对应 XYZ）显示所有正在发布的 tf 坐标系。这是理解机器人各部件空间关系的**第一工具**。

```bash
# 查看当前所有 tf 变换
rosrun tf view_frames
# 会在当前目录生成 frames.pdf 和 frames.gv

# 命令行查看 tf 树
rosrun rqt_tf_tree rqt_tf_tree
```

### 2. RobotModel — 显示机器人 3D 模型

```
Add → By display type → RobotModel
```

前提：有节点在发布 `robot_description` 参数（URDF 文件）和 `joint_states` topic。

### 3. LaserScan — 显示激光雷达扫描

```
Add → By topic → /scan → LaserScan
```

- `Size (m)`：每个激光点的大小
- `Style`：改为 `Flat Squares` 或 `Points` 更清晰
- `Color Transformer`：`Intensity` 可以用颜色表示距离强度

### 4. PointCloud2 — 显示点云

```
Add → By topic → /camera/depth/points → PointCloud2
```

- `Decay Time`：设为 0 表示持续积累，设为 N 表示 N 秒后消失
- `Style`：`Flat Squares`（俯看更好用）
- `Color Transformer`：选 `RGB` 可以看到彩色点云

### 5. Image — 显示相机图像

```
Add → By topic → /camera/color/image_raw → Image
```

rviz 不是 OpenCV 的替代品，它能让你在同一个窗口里把图像和激光点云叠在一起验证标定效果。

### 6. Path — 显示路径

```
Add → By topic → /move_base/NavfnROS/plan → Path
```

导航栈输出的全局路径。可以同时叠加局部路径 (`local_plan`) 和代价地图，排查导航问题。

### 7. Map — 显示占据栅格地图

```
Add → By topic → /map → Map
```

SLAM 或 map_server 发布的二维占据地图。

### 8. Marker 和 MarkerArray — 自定义可视元素

```cpp
#include <visualization_msgs/Marker.h>

ros::Publisher pub = nh.advertise<visualization_msgs::Marker>("marker", 10);

visualization_msgs::Marker m;
m.header.frame_id = "base_link";
m.ns = "demo";
m.id = 0;
m.type = visualization_msgs::Marker::CUBE;
m.action = visualization_msgs::Marker::ADD;
m.pose.position.x = 1.0;
m.scale.x = 0.2; m.scale.y = 0.2; m.scale.z = 0.2;
m.color.r = 1.0; m.color.a = 1.0;

pub.publish(m);
```

Marker 的类型有：ARROW、CUBE、SPHERE、CYLINDER、LINE_STRIP、TEXT_VIEW_FACING 等。这是调试时画箭头/方框/文字最灵活的方法。

---

## 一个典型调试会话（O'Kane 风格）

```bash
# 1. 启动 rviz
rosrun rviz rviz

# 2. 设置 Fixed Frame = "odom"

# 3. 添加 Display：
#    - TF            → 看坐标系关系（先加这个，其他显示都依赖正确的 tf）
#    - LaserScan     → 看雷达数据是否正常
#    - RobotModel    → 看模型是否和雷达数据对齐
#    - Path          → 看规划的路径

# 4. 如果 LaserScan 没显示 → rostopic hz /scan 确认有数据
# 5. 如果 LaserScan 歪了 → tf 树有断点，用 view_frames 查
# 6. 保存配置 → File → Save Config As → 给 team 共享
```

---

## 保存和加载配置文件

```bash
# 手动保存：rviz 菜单 File → Save Config As → 存为 my_config.rviz

# 在 launch 中自动加载
rosrun rviz rviz -d $(rospack find my_pkg)/rviz/my_config.rviz
```

配置文件是 YAML 格式，记录了所有 Display 及其参数。这是团队协作的基础——一个人调好 rviz 布局，其他人一键加载。

---

## Marker 技巧

### 调试用：在机器人前方画一个箭头

```cpp
void publishGoalArrow(ros::Publisher &pub, double x, double y) {
    visualization_msgs::Marker arrow;
    arrow.header.frame_id = "map";
    arrow.header.stamp = ros::Time::now();
    arrow.ns = "navigation";
    arrow.id = 0;
    arrow.type = visualization_msgs::Marker::ARROW;
    arrow.action = visualization_msgs::Marker::ADD;

    // 起点
    arrow.points.resize(2);
    arrow.points[0].x = 0;
    arrow.points[1].x = x;
    arrow.points[1].y = y;

    arrow.scale.x = 0.1;  // shaft diameter
    arrow.scale.y = 0.2;  // head diameter
    arrow.color.r = 1.0;
    arrow.color.a = 1.0;

    pub.publish(arrow);
}
```

> O'Kane 推荐的调试哲学：**"When in doubt, publish a Marker."**

---

## 常见错误

| 错误 | 原因 | 解决 |
|---|---|---|
| 全黑/空白 | Fixed Frame 不存在或没有 tf 数据 | 检查 `rostopic list` 和 tf 树 |
| LaserScan 位置明显不对 | tf 树有断点或坐标系名不匹配 | `rosrun tf view_frames` 查看 |
| 加上 RobotModel 后报错 | `robot_description` 参数或 `joint_states` topic 未发布 | 检查 launch 是否启动了 robot_state_publisher |
| rviz 启动特别慢 | 一个大型 PointCloud2 的 Decay Time = 0 导致内存爆 | 给 Decay Time 设个值如 5 秒 |
| 改了 display 参数没反应 | 有些参数只在 rviz 重启后才生效 | Save config 后重启 rviz |

---

## 一句话总结

**rviz 是 ROS 的"上帝的视角"——Fixed Frame 决定了你站在哪里看，Display 决定了你看到什么；设置 Fixed Frame + 加 TF Display 是所有调试的第一步。**
