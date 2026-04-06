# agent-project-template

一个用于长期、context-efficient agent 协作的项目模板。

适合：需要 agent 自驱完成的大型工程任务，任务周期跨越多个 Claude 会话。

---

## 核心设计思路

传统 LLM 会话是无状态的，跨会话后上下文全部丢失。
本模板通过**状态外化**解决这个问题：

```
CLAUDE.md（不变的规则层）
    +
STATUS.md（可变的状态层）
    =
agent 能在任意新会话中无损恢复
```

- `CLAUDE.md` 是 agent 的"操作手册"，告诉它怎么工作、遵循什么规范
- `STATUS.md` 是 agent 的"进度快照"，记录当前做到哪、下一步是什么
- 每次新会话，agent 读完这两个文件就能接着干，无需用户重新解释

---

## 快速开始

### 第一步：初始化项目

```bash
# 克隆或 fork 此模板
git clone https://github.com/your-username/agent-project-template.git my-project
cd my-project
```

### 第二步：填写项目配置

编辑以下文件中标有 `✏️` 的占位符：

| 文件 | 需要填写的内容 |
|------|--------------|
| `CLAUDE.md` | 任务描述、代码仓库地址、环境配置、网络约束 |
| `STATUS.md` | 第一个 milestone 目标、环境信息 |
| `docs/architecture.md` | 技术路线与里程碑拆解 |
| `.claude/commands/notify.md` | Webhook 地址（飞书 / 钉钉 / Slack） |

### 第三步：开始第一个任务

在 Claude Code 中打开项目目录，agent 会自动读取 `CLAUDE.md` 作为指令入口。

```bash
claude  # 启动 Claude Code
```

---

## 文件结构说明

```
agent-project-template/
├── CLAUDE.md                  ← Agent 执行手册（用户配置后交给 agent）
├── STATUS.md                  ← 进度快照（由 agent 维护，跨会话恢复锚点）
├── DEVLOG.md                  ← 开发日志（可选，记录关键事件流水账）
├── docs/
│   └── architecture.md        ← 技术架构设计（用户填写）
├── .claude/
│   └── commands/
│       └── notify.md          ← /notify skill（配置 Webhook 后启用）
└── README.md                  ← 本文件
```

项目启动后，agent 会按照 `CLAUDE.md` 中的工程规范在此目录下创建 `src/`、`tools/`、`evals/`、`runs/` 等子目录。

---

## 适用场景

- 机器学习训练框架搭建（本模板的原始用例）
- 复杂后端系统从零构建
- 多阶段数据工程 pipeline
- 任何需要 agent 持续迭代、跨越数天甚至数周的工程任务

---

## 设计原则

1. **状态外化**：agent 的记忆不存在上下文里，存在文件里
2. **人机分工清晰**：agent 自驱执行，用户只在决策点介入
3. **context 节约**：写文件比塞上下文便宜，重要信息落盘
4. **可观测**：STATUS.md 让用户随时知道 agent 在做什么、做到哪
