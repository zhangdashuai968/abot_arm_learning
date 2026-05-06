# P3-02 · MATLAB 激光雷达数据 + 栅格地图（E17-E18）

**阶段**：Phase 3
**预计耗时**：3-4 小时（合并版）
**前置**：P3-01 完成（MATLAB-ROS 联通）
**对应官方实验讲义**：
- 实验 17 MATLAB 获取激光雷达数据实验
- 实验 18 MATLAB 生成栅格地图实验
**优先级**：⚠️ **可选/简化**——栅格地图原理理解一遍即可，工程上直接用 ROS 的 map_server / cartographer

## 目标

理解栅格地图（occupancy grid）的本质：**每个格子是 0-1 之间的占用概率**，并能用 MATLAB 自己写一遍把 LaserScan 转成栅格地图的算法。

## 关键问题

1. 栅格地图分辨率（resolution）单位 m/cell，0.05 与 0.10 区别？
2. log-odds 表示法是什么？为什么不直接存概率而存对数？
3. LaserScan 的极坐标 (range, angle) 怎么转笛卡尔 (x, y)？
4. Bresenham 直线算法在栅格更新里干啥？
5. 一帧雷达数据更新一次栅格 vs 累计多帧加权，哪种更鲁棒？

## 执行步骤

### 1. MATLAB 读 /scan（实验 17）
```matlab
sub = rossubscriber('/scan');
msg = receive(sub, 5);
plot(msg)        % MATLAB 自带极坐标可视化
```

### 2. 极坐标转笛卡尔
- 自己写一段 (range, angle) → (x, y)
- 散点图画出一帧

### 3. 写栅格地图（实验 18）
- 设 resolution = 0.05 m/cell，地图 200×200
- 对每条激光：从原点到端点 Bresenham 走一遍，沿途格子标 free，端点格子标 occupied
- 用 log-odds 累加多帧

### 4. 与 ROS map_server 对照
- 跑 ROS 端 gmapping/cartographer 出图
- 与自己的 MATLAB 栅格地图叠加对比，看差异

## 产出

- `notes/E17-E18-栅格地图原理.md`：
  - 栅格地图概念图
  - log-odds 公式推导（不抄百科）
  - 自写 vs ROS 出图的对比
- 一份 MATLAB 脚本 `matlab_grid_map.m`

## 验收

- [ ] 自写脚本能产生一张可识别的栅格地图
- [ ] 关键问题 1-5 都有答案
- [ ] 能讲清"为什么用 log-odds"

## 跳过判定

时间紧可以只做 步骤 1. 和 2.（MATLAB 读 scan），跳 步骤 3. 和 4. 直接进 E21。

## 执行记录

<!-- 每次执行追加一段 -->
