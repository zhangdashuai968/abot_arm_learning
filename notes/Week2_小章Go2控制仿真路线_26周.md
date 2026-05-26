# 🛠️ 小章 26 周 Go2 控制仿真路线（v2 · Go2 适配版）

**接收人**：小章
**派发人**：欧阳俊龙
**派发日期**：2026-05-03（v2 重设：平台 Lite2→Go2 + 真田采集推秋季）
**周期**：26 周（2026-05-04 ~ 2026-10-25），完整覆盖 SCI Phase 1-4
**工时配额**：6h/周（4h 施肥主线 + 2h SCI 副线，仿真集中在 SCI 副线 + 部分施肥重叠）
**核心目标**：26 周后能独立支撑 SCI 端-眼-臂仿真验证，所有产出 commit 进 `tea_picking/simulation/`

---

## 0. 与 v1 的关键不同

| 维度 | v1（已归档） | v2（本版） |
|---|---|---|
| 平台 | 云深处 Lite2 | **宇树 Go2**（Isaac Lab 官方支持） |
| 周期 | 12 周（仅打地基） | **26 周**（覆盖 SCI Phase 1-4） |
| 与 SCI 耦合 | 弱 | **强**：直接交付到 `tea_picking/simulation/` |
| 真田采集 | 5 月底紧赶 | **秋季 10-11 月**（湛江茗禾），仿真侧时间宽裕 |
| 终点 | Isaac Sim 三件套同框 | **Sim2Real 真机准备完成 + 秋季采集联动** |

---

## 1. 五个里程碑（一眼看到底）

| Phase | 周数 | 验收点 | 截止日期 |
|---|---|---|---|
| 1 Gazebo 基础 | Week 2-4 | Go2 URDF + 6 轴臂在 Gazebo 立起来 | 2026-05-24 |
| 2 MoveIt2 P&P | Week 5-8 | Gazebo 抓固定方块 > 80% | 2026-06-21 |
| 3 Isaac Sim 入门 | Week 9-13 | Go2 + 机械臂 + 茶园 USD 三件套同框 | 2026-07-26 |
| 4 Isaac Lab RL 闭环 | Week 14-20 | 仿真采摘成功率 > 70% | 2026-09-13 |
| 5 Sim2Real 真机就绪 | Week 21-26 | Go2 真机 zero-shot 行走 + 秋季采集联动 | 2026-10-25 |

---

## 2. 与 `tea_picking/` 项目代码的对接

你的所有产出 **commit 到** `~/桌面/sci与ros2项目/tea_picking/simulation/`：

```
tea_picking/simulation/
├── go2_gazebo/          ← Phase 1-2 输出（URDF + world + moveit2_config）
├── isaac_sim/           ← Phase 3-4 输出（USD + Isaac Lab tasks）
└── sim2real/            ← Phase 5 输出（域随机化 + 合成数据生成）

tea_picking/data/synthetic/   ← Phase 5 合成数据落地点
tea_picking/docs/             ← 你的技术文档落地点
```

**每周日 commit 一次**，方便组会 review。

---

## 3. Phase 1：Go2 + Gazebo 基础（Week 2-4，每周 2h）

### Week 2（5/4-5/10，2h）
- [ ] `sudo apt install ros-humble-ros-gz`
- [ ] 跑通官方示例：Gazebo Garden + ros2_control 控制 R2D2
- [ ] 周报写 200 字：Gazebo 与 ROS1 时代差异

### Week 3（5/11-5/17，2h）
- [ ] clone 宇树官方仓：[unitreerobotics/unitree_ros2](https://github.com/unitreerobotics/unitree_ros2) + [unitree_mujoco](https://github.com/unitreerobotics/unitree_mujoco)
- [ ] Go2 URDF 加载进 Gazebo，看到机器人静态站立
- [ ] commit 到 `tea_picking/simulation/go2_gazebo/urdf/`

### Week 4（5/18-5/24，2h）
- [ ] 找到 6 轴机械臂 URDF（abot_arm_learning 已在用）
- [ ] 机械臂挂载到 Go2 背部（`<joint type="fixed">`）
- [ ] **验收**：Gazebo 中 Go2 + 背载机械臂同框；截图发周报

📚 [ROS 2 Gazebo Tutorial](https://docs.ros.org/en/humble/Tutorials/Advanced/Simulators/Gazebo/Gazebo.html) + 宇树官方文档

---

## 4. Phase 2：MoveIt2 + 6 轴臂 Pick-and-Place（Week 5-8，每周 2h）

### Week 5（5/25-5/31，2h）
- [ ] 装 MoveIt2 Humble
- [ ] MoveIt Setup Assistant 给 6 轴臂生成 config 包

### Week 6（6/1-6/7，2h）
- [ ] 跑通 MoveIt2 官方 Pick-and-Place 示例（适配你的臂）
- [ ] 理解 PlanningScene / RobotState / OMPL 三个核心概念

### Week 7（6/8-6/14，2h）
- [ ] 写 ROS2 节点：发布目标位姿 → MoveIt2 规划 → Gazebo 执行
- [ ] RViz2 可视化轨迹

### Week 8（6/15-6/21，2h）
- [ ] **验收**：Go2 站定 + 机械臂在 Gazebo 抓方块成功率 > 80%
- [ ] 写文档 `tea_picking/docs/moveit2_workflow.md`

📚 [MoveIt 2 Tutorials](https://moveit.picknik.ai/main/index.html) + 论文 ②-4 番茄 keypoint 的 IK 实现

---

## 5. Phase 3：Isaac Sim + Isaac Lab 入门（Week 9-13，每周 2h）

### Week 9（6/22-6/28，2h）
- [ ] 装 Omniverse Launcher + Isaac Sim 2024.1+（**预算 1 整天，别分散**）
- [ ] 跑通 Isaac Sim Hello World

### Week 10（6/29-7/5，2h）
- [ ] clone Isaac Lab 官方仓：[isaac-sim/IsaacLab](https://github.com/isaac-sim/IsaacLab)
- [ ] **关键**：Isaac Lab 已有 Go2 官方支持，跑通任务 `Isaac-Velocity-Flat-Unitree-Go2-v0`
- [ ] Go2 在 Isaac Sim 用官方 RL policy 走两步（无需自训）

### Week 11（7/6-7/12，2h）
- [ ] 制作 / 找一个茶园 USD 场景（Sketchfab 找茶树 + Blender 改）
- [ ] Go2 在场景里行走，相机视角能看到周围环境

### Week 12（7/13-7/19，2h）
- [ ] 6 轴机械臂 URDF 导入 Isaac Sim（URDF Importer 工具）
- [ ] 让机械臂在 Isaac Sim 里能动（Articulation Controller）

### Week 13（7/20-7/26，2h）
- [ ] **验收**：Go2 + 机械臂 + 茶园 USD 三件套同框，相机数据通过 ROS2 bridge 出来
- [ ] commit 到 `tea_picking/simulation/isaac_sim/go2_setup.py`
- [ ] 文档 `tea_picking/docs/isaac_sim_setup.md`

📚 [Isaac Lab Documentation](https://isaac-sim.github.io/IsaacLab/) + 论文 ④-2 Find the Fruit + ④-3 Lincoln FruitGym

---

## 6. Phase 4：Isaac Lab RL + 端-眼-臂闭环（Week 14-20，每周 2h）

### Week 14-15（7/27-8/9，4h）
- [ ] 看懂 Isaac Lab RL 训练框架（rsl_rl + skrl）
- [ ] 跑通 Go2 行走 RL 训练（Isaac Lab 自带任务）

### Week 16-17（8/10-8/23，4h）
- [ ] 设计 SCI 任务：「Go2 站定 + 机械臂接近茶芽 6DoF 位姿」
- [ ] 写自定义 task class（参考 Isaac Lab Manipulation 模板）
- [ ] 域随机化（光照 + 茶芽位姿 + Go2 微抖动）— 抄 ④-3 FruitGym 参数表

### Week 18（8/24-8/30，2h）
- [ ] 训练第一版 RL policy（PPO / SAC）
- [ ] **🎯 SCI 主线对齐节点**：Week 18 = SCI 算法跑通节点

### Week 19（8/31-9/6，2h）
- [ ] 加视觉感知到任务（茶芽检测 mock → 真模型）
- [ ] 与小俊的 T-YOLOv8n 模型对接（他周报会给权重）

### Week 20（9/7-9/13，2h）
- [ ] **验收**：仿真闭环采摘成功率 > 70%
- [ ] commit 到 `tea_picking/simulation/isaac_sim/isaac_lab_tasks/`

📚 Isaac Lab Manipulation Tutorials + 论文 ④-2 / ④-3 / ④-1 手眼综述

---

## 7. Phase 5：合成数据 + Sim2Real + 秋季采集联动（Week 21-26，每周 2h）

### Week 21-22（9/14-9/27，4h）
- [ ] Isaac Sim 大规模生成合成数据（10k+ 张茶园 + 茶芽 + Go2 视角）
- [ ] 自动 bbox/mask/keypoint 标注导出（COCO 格式）
- [ ] commit 到 `tea_picking/data/synthetic/`

### Week 23（9/28-10/4，2h）
- [ ] 与小俊对接：合成数据预训练 T-YOLOv8n，对比公开集 fine-tune

### Week 24（10/5-10/11，2h）
- [ ] Sim2Real 域适应：DODA / 风格迁移 / 域随机化加强
- [ ] 阅读论文 ④-6 DODA 实现细节

### Week 25（10/12-10/18，2h）
- [ ] **🎯 秋季真田采集准备**：为湛江茗禾茶园拍摄做仿真预演
- [ ] 写采集协议：相机标定 / 时间同步 / 标注规范

### Week 26（10/19-10/25，2h）
- [ ] **验收**：Go2 真机准备就绪 — RL policy 可 zero-shot 在真机走两步
- [ ] 与队长一起去湛江茗禾做第一次真田预采集

---

## 8. 每周 6h 怎么切

```
周一 19:00-20:00 (1h)  仿真学习（Phase 当周任务的前半）
周三 19:00-20:00 (1h)  仿真学习（Phase 当周任务的后半 + 卡点求救）
周四 全天     (3h)     施肥项目主线（路径规划 / 机械臂控制）
周六 19:00-20:30 (1.5h) abot_arm 带教 + amov_uav 答疑
周日 19:00-19:30 (0.5h) 周报撰写 + commit
```

**硬约束**：
- 仿真 2h / 周不可被施肥/带教挤占（这是底层投资，挤占 = 26 周白学）
- 卡点 > 30 min 必须问，求救对象：队长 / 王子恒 / 小余

---

## 9. 协同矩阵

| 协同对象 | 你做的事 | 他做的事 |
|---|---|---|
| **王子恒** | 仿真场景搭好后传给王做 ROS2 接口测试 | 王做真机平台调试，反馈真实参数给你校准仿真 |
| **小郭** | 5/23 后小郭承担数据可视化，你提供合成数据接口 | 他从 Isaac Sim 导出标注做 EDA |
| **小余** | 你需要末端机械臂精确 URDF + 装配位置 | 他做机械结构 SW 出图 + 装配体 |
| **小俊** | 你提供合成数据 + 仿真闭环环境 | 他给你 T-YOLOv8n 模型权重做仿真验证 |
| **欧阳（队长）** | 卡点求救 + 大方向决策 | 见导师拍板 + 论文写作 |

---

## 10. 五个关键风险

1. **Isaac Sim 装机踩坑** — 第一次装失败率高。**预算 1 整天，别分散**
2. **Isaac Lab Go2 任务版本兼容** — Isaac Lab 更新很快，按官方 release tag 锁版本
3. **施肥项目临时插件挤占** — 4h/周施肥是底线，**仿真 2h 不可被吞**
4. **秋季采集准备不够** — Phase 5（Week 21-26）必须按时，否则真田采集计划崩
5. **Phase 4 RL 训练失败** — 备案：用 imitation learning 替代 RL（参考 ④-5 CMU Pepper）

---

## 11. 第一周（5/4-5/10）立刻要做

```bash
# Day 1 晚（5/4 周一，1h）
sudo apt install ros-humble-ros-gz
# 通读 ROS2 Gazebo 官方教程

# Day 3 晚（5/6 周三，1h）
# 跑通 R2D2 spawn 示例
# 通读宇树 unitree_ros2 README
# 周报写 Phase 1 启动情况
```

---

## 12. 26 周里程碑日历（贴墙上 / 设钉钉日历）

```
Week 4   (2026-05-24)  Go2 + 6 轴臂 URDF 在 Gazebo 立起来
Week 8   (2026-06-21)  MoveIt2 抓方块 > 80%
Week 13  (2026-07-26)  Isaac Sim 三件套同框（Go2 + 臂 + 茶园）
Week 20  (2026-09-13)  Isaac Lab RL 闭环采摘 > 70%
Week 26  (2026-10-25)  Sim2Real 真机就绪 → 湛江茗禾秋季采集
```

---

## 13. 关联文件位置

- **项目代码框架**：`~/桌面/sci与ros2项目/tea_picking/`（已有 G-TFA 模块设计）
- **baseline 文献库**：`~/桌面/sci与ros2项目/Week1_文献Zotero卡片汇总.md`
- **baseline BibTeX**：`~/桌面/sci与ros2项目/Week2_baseline.bib`
- **仓库下载清单**：`~/桌面/sci与ros2项目/Week2_仓库与数据集清单.md`
- **拍板决议**：`~/桌面/sci与ros2项目/Week2_导师待拍板清单.md`
- **v1 路线（归档）**：`~/桌面/sci与ros2项目/归档/2026-05-03_v1_小章控制仿真学习路线_12周_Lite2版.md`

---

> **加油。Lite2 留给施肥课题，Go2 + Isaac Lab 是 SCI 主线的硬底座。**
> **26 周后你就是团队仿真天花板，电控组长候选身份的硬底座彻底铺好。**
