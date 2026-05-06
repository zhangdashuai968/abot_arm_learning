# E32 · ROS 视觉伺服通信（Phase 4 收官 / SCI 起跳）

**阶段**：Phase 4 出口
**预计耗时**：5-6 小时
**前置**：E31 检测、P4-01 标定、E11/E12 IK
**对应官方实验讲义**：`~/桌面/abot开源机器人/v01.01.01.0000/实验32_ROS视觉伺服通信实验.md`

## 目标

把 **检测 → 3D 位置 → IK → 抓取** 整个闭环串起来：深度相机看到红色方块 → 算出方块在 base_link 系下的 (x, y, z) → 机械臂自己伸过去 → G3P 二指夹起来。

**这是 Phase 4 出口，也是 SCI 茶园采摘"嫩芽伺服采摘"的最小可行版本（MVP）。**

## 关键问题

1. 像素坐标 (u, v) → 相机系 (X, Y, Z) 需要哪两个东西？（深度 + 内参）
2. 相机系 → 末端系 → 基座系，三步变换分别从哪儿来？（手眼标定 + tf）
3. 视觉伺服分 PBVS（基于位姿）和 IBVS（基于图像），本任务用哪种？
4. 检测帧率 15 FPS，机械臂控制 50 Hz，怎么对齐？（latched topic / 取最近一帧）
5. 抓取失败的常见原因 Top 3（深度噪声 / 标定误差 / 夹爪定位偏置）？

## 执行步骤

### 1. 拿 3D 位置
```python
# 订阅 detections + aligned_depth
def get_3d(u, v, depth_img, K):
    z = depth_img[v, u] * 0.001  # mm → m
    x = (u - K[0,2]) * z / K[0,0]
    y = (v - K[1,2]) * z / K[1,1]
    return np.array([x, y, z])    # 相机系
```

### 2. 变换到 base_link
```python
# 用 tf2 把点从 camera_color_optical_frame 变到 base_link
listener = tf2_ros.Buffer()
trans = listener.lookup_transform("base_link", "camera_color_optical_frame", rospy.Time(0))
pt_base = do_transform_point(pt_camera, trans)
```

### 3. 调 MoveIt 抓取
```python
# 1. 预接近：目标上方 10 cm
# 2. 下降到目标 + 抓取偏置
# 3. 闭夹爪
# 4. 提起来
group.set_pose_target([x, y, z+0.10, 0, math.pi, 0])
group.go()
group.set_pose_target([x, y, z+0.02, 0, math.pi, 0])
group.go()
gripper_close()
```

### 4. 跑通"看到 → 抓住"
- 桌面放红色方块
- 启动 `detect.launch` + `manipulation.launch`
- 测试 10 次，记录成功率

### 5. 失败分析（必做）
对每次失败：
- 是没看到？（检测漏了）
- 看到位置错？（深度噪声 / 标定误差）
- 位置对但抓不住？（夹爪偏置 / 物体打滑）
分别归因，写 `notes/E32-失败模式分析.md`

## 产出

- ROS 包 `abot_grasp/`：
  - `scripts/visual_servo_grasp.py`
  - `launch/grasp_demo.launch`
- 一段抓取演示视频（≥ 5 次成功）
- `notes/E32-视觉伺服.md`：3D 计算公式 + tf 链路 + 失败模式
- **Phase 5 直接复用**：把"红色方块"换成"茶叶嫩芽"，整条链路代码不动

## 验收（Phase 4 出口）

- [ ] 抓取成功率 ≥ 50%（占位类，简单场景）
- [ ] 关键问题 1-5 都有答案
- [ ] 失败模式分析写完
- [ ] 与 Phase 5 SCI 联动的"换数据集即可"路径打通

## 与 SCI 的接口

> Phase 4 的代码骨架直接喂 Phase 5：
> - `abot_vision/best.pt` → `tea_bud_v1.pt`（换权重）
> - `abot_grasp/grasp_demo.launch` → `tea_pluck.launch`（改抓取偏置 / 夹爪策略）
> - 失败模式分析报告 → SCI 论文实验章节"误差源分析"小节素材

## 执行记录

<!-- 每次执行追加一段 -->
