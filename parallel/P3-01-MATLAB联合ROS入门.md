# P3-01 · MATLAB / Simulink 联合 ROS 入门（E15-E16）

**阶段**：Phase 3
**预计耗时**：3-4 小时（合并版）
**前置**：本机有 MATLAB R2020b+，装了 ROS Toolbox
**对应官方实验讲义**：
- 实验 15 MATLAB 联合 ROS 控制移动实验
- 实验 16 Simulink 联合 ROS 控制移动实验
**优先级**：⚠️ **可选/简化**——若与 SCI 课题无强关联，2 小时通读后跳过即可，不深挖

## 目标

知道 MATLAB / Simulink 是怎么"作为客户端"接入 ROS Master 的，能用 Simulink 拖一个最小控制律发布 `/cmd_vel`。**不必精通**。

## 关键问题

1. MATLAB ROS Toolbox 的 `rosinit` 在干啥？等价于 ROS 的什么命令？
2. Simulink 的 ROS Publish/Subscribe block 与 C++/Python 节点有何不同？
3. 工业界是否真的在车上跑 MATLAB？（一般不，多用于算法快速验证 / 研究阶段）
4. 用 Simulink 写好的控制律，怎么导出成 C++ 节点？（Embedded Coder）
5. 与 ROS 联合的 MATLAB 仿真，与纯 Gazebo / Stage 仿真，哪种更轻？

## 执行步骤（精简版）

### 1. 环境验证
```matlab
rosinit('http://<工控机IP>:11311')
rostopic list
```

### 2. MATLAB 走方框（实验 15）
- 写一个 MATLAB 脚本发布 `/cmd_vel`
- 跑 30 秒看车走出大概的方框

### 3. Simulink 拖一个（实验 16）
- 新建模型 → 拖 ROS Publish block → 设置 topic 和消息
- 用 Constant + Mux 拼一个 Twist 消息
- 仿真运行 → 看车动

## 产出

- `notes/E15-E16-MATLAB联合ROS.md`：一段记录 + 一张 Simulink 截图
- 不写 cheatsheet（用得少，不值得）

## 验收（降低要求）

- [ ] 跑通 MATLAB 发布 `/cmd_vel` 一次
- [ ] 拖一个最小 Simulink 模型并仿真成功
- [ ] 关键问题 1, 3, 4 有答案

## 跳过判定

如果你的 SCI 茶园采摘流程里**完全不用 MATLAB**，本任务可以跳过，直接进 P3-02 / E21。

## 执行记录

<!-- 每次执行追加一段 -->
