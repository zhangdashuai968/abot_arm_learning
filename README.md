# abot 机械臂车学习项目

> 启动日期：2026-04-30
> **Ownership（2026-04-30 决策）**：**主执行 = 小章带教 + 小沈/小郭 轮流参与**；队长仅 review 月报 + Phase 4 (E29-E32) + Phase 5（SCI 联动）亲自抓
> 周期预算：3-4h/周（嵌套在 SCI 论文项目内，与硬件熟悉/采摘流程验证共用时间盒）
> 终点目标：**吃透 32 个官方实验 + 能独立写茶园采摘场景的原创 launch / 节点**，作为 SCI 茶园采摘视觉感知课题的硬件落地能力底座
> 决策依据：见 `~/桌面/任务分发决策_2026-04-30.md` D2 节

## 这是什么

中科深谷《基于深度视觉抓取移动作业开源机器人 v01.01.01.0000》的学习落地工作区。
平台 = Zeus S2 重载底盘 + ROCR6 六轴协作机械臂 + 深度相机（Orbbec） + RPLIDAR A3M12 + G3P 柔性夹持器，软件栈 **ROS1**。

## 目录结构

| 目录 | 用途 | 状态 |
|---|---|---|
| `roadmap.md` | 6 阶段总路线图，每阶段目标 / 产出 / 验收 | ✅ 已就绪 |
| `parallel/` | 独立子任务 spec + 学习笔记，可丢给另一窗口并发执行；命名 `E{实验号}-{实验名}.md` 或 `P{阶段}-{编号}-{任务}.md`，文件内追加执行记录 | ✅ 27 个文件 |
| `notes/` | 教材精读 / 概念梳理笔记（Phase 1 笔记当前位于 `parallel/E03-E09`） | 待填充 |
| `logs/` | 调试 / 跑通 / 调优日志（按日期） | 待填充 |
| `cheatsheets/` | 速查表（启动链路 / 标定参数 / TF tree / launch 索引） | 雏形已建 |

## 当前位置

**Phase 1 — 认知与 ROS 基础**（进行中）

- [ ] E01 本体组成与认知（`parallel/E01-本体组成与认知.md`）
- [ ] E02 系统组成与认知（`parallel/E02-系统组成与认知.md`）
- [ ] E03-E09 ROS 基础 7 件套（统一 spec：`parallel/P1-03-ROS基础7件套.md`；逐实验详细笔记：`parallel/E03-E09` 系列）
- [ ] 启动链路 cheatsheet 雏形（`cheatsheets/启动链路.md`）

## parallel/ 文件清单

### Phase 1 · 认知与 ROS 基础
| 文件 | 定位 |
|---|---|
| `E01-本体组成与认知.md` | 执行 spec（实物观察 + 手绘结构图） |
| `E02-系统组成与认知.md` | 执行 spec（软件拓扑 + 启动链路） |
| `P1-03-ROS基础7件套.md` | 合并 spec（E03-E09 执行入口） |
| `E03-乌龟仿真.md` | 📖 详细笔记（Topic 入门，含 C++ 完整代码） |
| `E04-消息使用.md` | 📖 详细笔记（自定义 msg、CMakeLists 四步配置） |
| `E05-服务通信.md` | 📖 详细笔记（Service Server/Client，Topic vs Service 对比） |
| `E06-动作使用.md` | 📖 详细笔记（Action Server/Client，Preemption 机制） |
| `E07-rviz工具.md` | 📖 详细笔记（Fixed Frame 原理，8 种 Display 类型） |
| `E08-rqt工具.md` | 📖 详细笔记（rqt_graph/plot/console/reconfigure 调试流） |
| `E09-串口绑定.md` | 📖 详细笔记（udev 规则四步法，全设备 idVendor/idProduct 速查） |

### Phase 2 · 底盘 + 机械臂运动学
| 文件 | 定位 |
|---|---|
| `E10-ROS控制底盘移动.md` | 执行 spec（/cmd_vel 控制，走方框测误差） |
| `E11-运动学正解.md` | 执行 spec（DH 表 + 手推正解 + ROS tf 验证） |
| `E12-运动学逆解.md` | 执行 spec（MoveIt IK + 手算逆解 + 奇异点观察） |
| `E13-本体结构建模.md` | 执行 spec（URDF 解读 + 改 joint limit） |
| `E14-避障传感器.md` | 执行 spec（传感器清单 + 量程精度测量 + 布局图） |

### Phase 3 · SLAM + 建图导航
| 文件 | 定位 |
|---|---|
| `P3-01-MATLAB联合ROS入门.md` | 合并 spec（E15-E16，⚠️ 可选） |
| `P3-02-MATLAB激光雷达建图.md` | 合并 spec（E17-E18，⚠️ 可选） |
| `E21-激光SLAM闭环检测.md` | 执行 spec（karto_slam + 闭环触发观察） |
| `E22-G2O图优化.md` | 执行 spec（g2o demo + pose graph + 信息矩阵） |
| `E23-Cartographer融合建图.md` | 执行 spec（LiDAR+IMU+Odom 三源融合 + 调参对比） |
| `E24-导航与避障.md` | 执行 spec（move_base + AMCL + 避障测试） |

### Phase 4 · 视觉感知 + 抓取
| 文件 | 定位 |
|---|---|
| `P4-01-相机标定.md` | 合并 spec（E25-E26：内参 + 手眼标定） |
| `E27-相机数据采集.md` | 执行 spec（深度相机四路数据流 + 采集脚本） |
| `E28-颜色识别.md` | 执行 spec（HSV 阈值法 + OpenCV 节点） |
| `P4-02-数据集与模型训练.md` | 合并 spec（E29-E30：标注 + YOLOv8 训练） |
| `E31-实时目标检测.md` | 执行 spec（YOLO ROS 部署，≥15FPS） |
| `E32-ROS视觉伺服通信.md` | 执行 spec（Phase 4 出口：检测→3D→IK→抓取闭环） |

### Phase 5-6 · 待规划
Phase 5（SCI 联动茶园采摘）和 Phase 6（陆空协同方案）spec 将根据 Phase 4 出口成果编写。

## 工程代码

> **学习是"课堂"，代码是"战场"** — 学完实验后，到实际工程仓库看真机部署代码。

👉 [small-car](https://github.com/zhangdashuai968/small-car) — ABOT M1 ARM 小车真机 ROS 工作区，包含 LIDAR、深度相机、IMU 融合定位、机械臂控制、航线巡逻、视觉抓取等完整工程实现。学完 roadmap 的实验后，来这里看"真实项目怎么写的"。

## 与 SCI 茶园采摘课题的映射

| SCI 子能力 | 对应实验 | 在本项目的位置 |
|---|---|---|
| 视觉感知（茶叶检测） | E27-E31（采集→数据集→YOLO→检测） | Phase 4 |
| 末端伺服采摘 | E25-E26（标定）+ E32（视觉伺服） | Phase 4 |
| 移动到茶垄 | E10, E14-E18, E21-E24（底盘+SLAM+导航） | Phase 2-3 |
| 整机集成 | 自研 launch + 二指夹爪策略 | Phase 5 |
| 与机械狗协同（如走到这一步） | UAV/狗到位 → AGV 抵达后采摘 | Phase 6 |

## 关联资源

- 平台资料根目录：`~/桌面/abot开源机器人/v01.01.01.0000/`
- 32 个实验讲义：`~/桌面/abot开源机器人/v01.01.01.0000/实验*.md`（缺 19、20）
- 培训演示 PPT：`~/桌面/abot开源机器人/v01.01.01.0000/01.开发文档/基于深度视觉抓取移动作业开源机器人-培训演示.pptx`
- 产品介绍/接口/实验指导书：`~/桌面/abot开源机器人/v01.01.01.0000/01.开发文档/适配ROCR6自主机械臂/txt/`
- 代码仓库：`gitee.com/vstc-tech/complex_robot`
- 硬件原理图：`~/桌面/abot开源机器人/v01.01.01.0000/03.硬件资料/01.电器资料/`
- 结构 SolidWorks：`~/桌面/abot开源机器人/v01.01.01.0000/03.硬件资料/02.结构资料/移动抓取_ros/`

## 学习用书

- **主要教材**：Jason O'Kane《A Gentle Introduction to ROS》（免费 PDF，`e:\abot_study\agitr-small.pdf`）
- **中文参考**：胡春旭《ROS机器人开发实践》（机械工业出版社，ISBN 9787111598237）

## 与并行项目的关系

- **SCI 茶园采摘**（主线）：本项目是其硬件落地能力底座，时间盒共用
- **[small-car](https://github.com/zhangdashuai968/small-car)**（工程落地）：学完理论后的实战代码库，ABOT M1 真机 ROS 工作区
- **视觉 SLAM 长期项目**：Phase 3（激光 SLAM）与之有 30% 重叠，可互相喂内容
- **机械狗自研**：Phase 6 协同方案对接
- **amov_uav_learning**（无人机）：Phase 6 陆空协同时对接
