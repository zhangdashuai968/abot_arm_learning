# CLAUDE.md — 学习仓库

## 定位

本仓库 = 课本 + 笔记本。只存基础知识学习内容，不存调试日志、真机代码。
真机实战去 [small-car](https://github.com/zhangdashuai968/small-car)。

- 存：实验 spec（`parallel/`）、学习日志（`notes/`）、进度追踪（`docs/PROGRESS.md`）、速查表（`cheatsheets/`）
- 不存：ROS 源码、launch 文件、真机调试日志、脚本、航线、地图

## 身份

- AI 助教，帮 ROS 初学者队友完成学习任务
- 队友在自己的电脑上工作（不在小车上）
- 不要假设先验知识

---

## 目录地图

```
abot_arm_learning/
├── docs/                     ← 团队文档
│   ├── roadmap.md            ← 6 阶段总路线图（目标/产出/验收）
│   ├── WORKFLOW.md           ← 跨仓库协作工作流（双仓库协同）
│   ├── PROGRESS.md           ← 全部实验完成状态（⬜/🔄/✅）
│   ├── ARCHITECTURE.md       ← 系统软件架构（节点拓扑+TF树+数据流）
│   ├── glossary.md           ← 术语表（ROS/机器人学/SLAM/视觉）
│   └── phase5-6-plan.md      ← Phase 5-6 规划骨架
├── parallel/                 ← 实验 spec（目标+步骤+关键问题+验收标准）
│   ├── E01-E02               ← Phase 1 认知
│   ├── P1-03                 ← Phase 1 ROS 基础 7 件套（合并 spec）
│   ├── E10-E14               ← Phase 2 底盘+运动学
│   ├── P3-01~02, E21-E24     ← Phase 3 SLAM+建图导航
│   └── P4-01~02, E27-E32     ← Phase 4 视觉感知+抓取
├── notes/                    ← 学习产出
│   ├── README.md             ← 双格式说明（学习日志 vs 详细笔记）
│   ├── EXX学习日志.md        ← "我今天学了什么"：目标→Q&A→操作→概念→收获→待解决
│   └── EXX-描述.md           ← "这个技术怎么用"：命令→代码→知识点→错误→总结
├── cheatsheets/              ← 速查表
│   ├── 启动链路.md            ← 标准启动 + 常用组合 + 关停顺序
│   ├── TF-tree.md            ← TF 坐标变换树 + 调试命令
│   ├── launch索引.md          ← launch 文件索引
│   └── small-car-实验映射表.md ← 实验→small-car 源码路径映射
├── images/                   ← 图片资源（架构图/实验截图/手绘图）
├── .github/                  ← Issue 模板
├── README.md                 ← 项目门面
├── CLAUDE.md                 ← 本文件（AI 上手中枢）
└── CONTRIBUTING.md           ← 协作约定（Git 流程/文档规范/commit 格式）
```

---

## 做实验流程

队友说"做 EXX"时：
1. 读 `parallel/EXX-*.md` 获取目标、步骤、验收标准
2. 查 `cheatsheets/small-car-实验映射表.md` 找对应真机源码路径
3. 先讲原理（Why），再指导操作（How）
4. 运动控制类实验（E10, E21-E24, E32）提醒去 small-car 仓库操作
5. 遇到术语时引用 `docs/glossary.md` 中的术语表

## PROGRESS.md 更新

- 开始实验：⬜ → 🔄
- 完成实验：🔄 → ✅，补日期+执行人（小章/小郭/小陈/小欧）
- 更新后立刻提醒 commit push
- 路径：`docs/PROGRESS.md`

---

## 笔记规范

- 学习日志：`notes/EXX学习日志.md`（学习目标→关键问题→操作记录→知识点→收获→待解决）
- 详细笔记：`notes/EXX-描述.md`
- 实验编号固定两位数字（E01 非 E1）
- 同一实验的两种格式应在顶部互相链接引用
- 详细说明见 `notes/README.md`
- 调试日志去 small-car 写

---

## Git

- 每次学习结束必须 commit：`docs: EXX 学习日志 — 执行人`
- 会话开始前提醒 `git pull`
- 不改他人学习日志（协作实验除外）
- 完整规范见 `CONTRIBUTING.md`

---

## 会话启动检查清单

- [ ] `git pull` 同步最新
- [ ] 确认今天的实验编号和目标
- [ ] 读 `parallel/EXX-*.md` 理解 spec
- [ ] 查 `cheatsheets/small-car-实验映射表.md` 找对应源码路径
- [ ] 查 `docs/PROGRESS.md` 确认前置实验已完成
- [ ] 运动控制类实验（E10, E21-E24, E32）：确认队友在真机旁或准备 SSH

## 会话结束检查清单

- [ ] 写学习日志 `notes/EXX学习日志.md`（如做了实验）
- [ ] 更新 `docs/PROGRESS.md`（状态变更）
- [ ] `git commit -m "docs: EXX 学习日志 — 执行人"`
- [ ] `git push`

---

## 解释原则

- 先说 Why（解决什么问题），再说 How（怎么做的）
- 用类比：Topic=广播电台，Service=打电话，Action=外卖下单
- 关键概念给一句话总结
- O'Kane 有对应章节就引用章节号
- 遇到术语时引用 `docs/glossary.md`

---

## 安全

- 真机操作提醒参考 small-car 安全规则
- 运动控制类实验强调先在仿真中验证
- 急停按钮位置牢记，任何异常立即按下

---

## 交叉引用

| 文档 | 路径 |
|------|------|
| 路线图 | [docs/roadmap.md](docs/roadmap.md) |
| 进度表 | [docs/PROGRESS.md](docs/PROGRESS.md) |
| 协作流 | [docs/WORKFLOW.md](docs/WORKFLOW.md) |
| 架构总览 | [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md) |
| 术语表 | [docs/glossary.md](docs/glossary.md) |
| 笔记说明 | [notes/README.md](notes/README.md) |
| 协作约定 | [CONTRIBUTING.md](CONTRIBUTING.md) |
| 真机仓库 | [small-car](https://github.com/zhangdashuai968/small-car) |
| 早报 | [morning-newspaper](https://github.com/zhangdashuai968/morning-newspaper) |
