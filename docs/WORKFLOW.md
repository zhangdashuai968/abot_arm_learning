# 团队协作工作流

> 适用仓库：abot_arm_learning（学习路线）+ small-car（真机代码）
> 团队成员：小章(队长)、小郭、小陈、小欧
> 更新时间：2026-05-26

---

## 两个仓库的定位

### 一句话

| 仓库 | 是什么 | 在哪个机器上 | 核心职责 |
|------|--------|-------------|----------|
| `abot_arm_learning` | 课本 + 笔记本 | **你的电脑** | 学习基础知识 |
| `small-car` | 兵器库 + 航海日志 | **Jetson Nano 小车** | 真机调试实战 |

### 什么东西放哪个仓库

| 内容 | 放哪里 | 说明 |
|------|--------|------|
| 实验 spec（执行指南） | `abot_arm_learning/parallel/` | 做实验前阅读，告诉你步骤和验收标准 |
| 学习日志 | `abot_arm_learning/notes/` | 学完实验后写：学到了什么知识点 |
| 进度追踪 | `abot_arm_learning/docs/PROGRESS.md` | 勾选实验完成状态 |
| 速查表 | `abot_arm_learning/cheatsheets/` | 启动链路、TF tree、launch 索引 |
| 实验→源码映射 | `abot_arm_learning/cheatsheets/small-car-实验映射表.md` | 实验对应 small-car 哪个源文件 |
| ROS 源码 | `small-car/src/` | launch 文件、C++/Python 节点 |
| 运行脚本、航线、地图 | `small-car/scripts/` | |
| 真机调试日志 | `small-car/logs/` | 结构化调试日志，有模板 |
| 真机随手记录 | `small-car/小车调节日志.txt` | 在车上调参时的快速记录 |
| 踩坑手册 | `small-car/TROUBLESHOOTING.md` | 遇到 bug 及解法，全队共享 |
| 会话报告 | `small-car/reports/` | 每次真机操作后的报告（人工 + AI） |

### 一个具体例子：小郭做 E23（Cartographer 融合建图）

```
[在自己电脑上]
1. cd abot_arm_learning
2. git pull                                    # 拉取最新 spec
3. 读 parallel/E23-Cartographer融合建图.md     # 理解目标、关键问题
4. 打开 Claude Code，AI 帮你理解 Cartographer 原理
5. 写预习笔记到 notes/（可选）

[SSH 到小车上]
6. ssh abot@192.168.36.46
7. cd ~/robot_ws && git pull                   # 拉取最新代码
8. 对照 cheatsheets/small-car-实验映射表.md，找到 cartographer_slam.launch
9. 打开 Claude Code（在 small-car 目录），AI 帮你改参数
10. roslaunch abot bringup.launch
11. roslaunch abot slam/cartographer_slam.launch
12. 测试、调参，随手记到 小车调节日志.txt
13. 写结构化调试日志到 logs/2026-05-27_E23_Cartographer调优.md
14. auto-sync.sh 自动 commit + push 代码变更到 GitHub

[回到自己电脑上]
15. cd abot_arm_learning
16. 写学习日志 notes/E23学习日志.md  （学到了什么知识点）
17. 更新 docs/PROGRESS.md：E23 ⬜ → ✅
18. git commit + push

[在 small-car 仓库]
19. 写会话报告 reports/2026-05-27_小郭_E23_Cartographer调优.md      # 人工版
20. 写会话报告 reports/2026-05-27_小郭_E23_Cartographer调优_AI.md   # AI版
21. git commit + push
```

---

## 一次标准协作会话

### 开始前（5 分钟）

1. 两个仓库都 `git pull` 同步最新代码
2. 检查小车状态：电池电压 ≥ 11.5V、ROS Master 可达（`rostopic list` 有输出）
3. 明确今天的实验编号（如 E23）和目标
4. 在对应仓库目录下打开 Claude Code（或 Codex），AI 自动读取 CLAUDE.md

### 进行中

1. 先阅读 `parallel/EXX-*.md` 理解实验 spec
2. 对照 `cheatsheets/small-car-实验映射表.md` 找到对应源码路径
3. 在真机上操作，实时记录日志
4. **每完成一个步骤就 git commit + push**（不要攒到最后）
5. 遇到问题先查 small-car 的 `TROUBLESHOOTING.md`

### 结束后（10 分钟）

1. 更新 `docs/PROGRESS.md`（如实验完成：⬜ → ✅，填日期和执行人）
2. 写学习日志（`notes/EXX学习日志.md`）—— 学到了什么
3. 在 small-car 写结构化调试日志（`logs/`）+ 两份会话报告（`reports/`）
4. **两个仓库最终 git push**
5. 关停小车：机械臂回 home → Ctrl+C 所有 ROS 进程 → 断电

---

## Git 规范

> 详细的 Git 流程、commit 格式、禁止事项见 [CONTRIBUTING.md](../CONTRIBUTING.md)
> 下面仅补充跨仓库独有的约定。

### 跨仓库 commit 时机

- 学习仓库（abot_arm_learning）和真机仓库（small-car）**分开 commit**，不要混在一个 commit 里
- 学习日志写完立即 commit 学习仓库
- 真机调试日志写完立即 commit 真机仓库

---

## AI 工具使用指南

> 详细的 AI 使用技巧、禁忌见 [CONTRIBUTING.md](../CONTRIBUTING.md)

### 基本用法

1. 在仓库目录打开 Claude Code，AI 自动读取 `CLAUDE.md`
2. 会话开始时告诉 AI："今天做 E23 实验，我是小郭"
3. 会话结束时告诉 AI："帮我生成 AI 报告初稿"
4. 你检查 AI 报告、修正不准确的地方，然后提交

---

## 报告体系

每次真机调试/开发会话结束后，在 small-car 的 `reports/` 目录产出两份报告：

| | 人工报告 | AI 报告 |
|---|---|---|
| **读者** | 人类队友 | AI 助手（下次会话读取） |
| **填写人** | 你自己 | AI 生成初稿，你确认 |
| **耗时** | 5 分钟 | AI 自动生成 |
| **命名** | `YYYY-MM-DD_执行人_简述.md` | `YYYY-MM-DD_执行人_简述_AI.md` |

> 详细说明见 small-car 仓库的 `reports/README.md`

---

## 沟通规范

| 场景 | 渠道 |
|------|------|
| 日常讨论 | 微信群 |
| 正式记录、任务跟踪 | GitHub Issue |
| 紧急问题 | 直接群语音 |
| 每周进度汇总 | 队长基于 PROGRESS.md 发布周报 |

---

## 快速参考卡片（可打印贴在小车旁）

### 开机
- [ ] 电池 ≥ 11.5V
- [ ] 网线连接小车主控
- [ ] `git pull`（两个仓库）

### 关机
- [ ] 机械臂回 home
- [ ] Ctrl+C 所有 ROS 进程
- [ ] `git push`（两个仓库）
- [ ] 写报告（small-car `reports/`）
- [ ] 更新 PROGRESS.md（如实验完成）
- [ ] 断电

---

## 修订记录

| 日期 | 变更 |
|------|------|
| 2026-05-26 | 初版 |
