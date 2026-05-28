# 协作指南

> 团队成员 + Claude Code 共用的贡献约定。
> 仓库定位见 [CLAUDE.md](CLAUDE.md)，路线图见 [docs/roadmap.md](docs/roadmap.md)。

## 1. 平台事实

| 项 | 值 |
|----|----|
| 仓库类型 | 纯文档仓库（无源码） |
| 学习平台 | 中科深谷 abot 开源机器人 (ROCR6 + Zeus S2) |
| 软件栈 | ROS1 Melodic / Noetic |
| 教材 | O'Kane《A Gentle Introduction to ROS》 |
| 真机代码 | [small-car](https://github.com/zhangdashuai968/small-car) |

## 2. Git 流程

```bash
git pull --rebase
# ... 改动 ...
git add <具体文件>
git commit -m "docs: EXX 学习日志 — 执行人"
git push
```

### 提交信息规范

| 前缀 | 用途 | 示例 |
|------|------|------|
| `docs:` | 学习日志/笔记 | `docs: E03 学习日志 — 小章` |
| `spec:` | 实验 spec 更新 | `spec: E10 增加验收标准` |
| `progress:` | 进度更新 | `progress: E05 ⬜→✅ 小郭 2026-05-28` |
| `chore:` | 仓库维护 | `chore: 更新 CLAUDE.md 目录地图` |

### 禁止事项

- 禁止 `git push --force` 到 master
- 禁止修改他人的学习日志（协作实验除外）
- 禁止在 commit 中包含密码、token

## 3. 文档规范

### 学习日志 (`notes/EXX学习日志.md`)

标准模板：
```markdown
# EXX 学习日志
## 学习日期
## 执行人
## 学习目标
## 关键问题及回答
## 终端操作记录
## 核心概念总结
## 学习收获
## 待解决问题
```

### 详细笔记 (`notes/EXX-描述.md`)

标准模板：
```markdown
# EXX · 实验名称
## 来源
## 阶段
## 核心概念
## 关键问题
## 核心命令
## 代码示例
## 关键知识点
## 常见错误
## 一句话总结
```

### 实验 spec (`parallel/EXX-描述.md`)

标准模板：
```markdown
# EXX · 实验名称
## 目标
## 前置条件
## 关键问题
## 步骤
## 产出
## 验收标准
## 安全注意
```

## 4. 笔记双格式说明

见 [notes/README.md](notes/README.md)

## 5. 交叉引用规范

- 笔记和 spec 之间必须互相链接
- 相关实验之间添加"前置实验 / 后续实验"链接
- 引用 cheatsheets 中的速查表时使用相对链接
- 引用 small-car 真机仓库时使用完整 GitHub URL
