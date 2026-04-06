# Agent 执行手册

此文档是该项目的 agent 执行手册，LLM 可以以此文档为锚点。

---

## 上下文恢复协议（每次新会话 / clear 后必读）

**触发条件：** 用户执行 `/clear` 或新会话开始时，agent 必须立即执行以下步骤，不得依赖上下文记忆：

1. 读取 `STATUS.md` —— 了解当前阶段、已完成工作、下一步任务、阻塞项
2. 根据 STATUS.md 的"下一步"章节，确认即将执行的任务
3. 向用户简要汇报恢复结果："已恢复到 [当前阶段]，准备执行 [下一步]"，然后继续工作

**两个锚点文档的分工：**
- `CLAUDE.md`（本文件）：规则 / 框架 / 工作方式，**由用户维护**，喂给 agent 作为入口
- `STATUS.md`：当前进度快照，**由 agent 维护**，每次 milestone 后更新

---

## 任务描述

> ✏️ **[在此填写项目目标]**
>
> 示例：实现一个用强化学习持续强化 LLM coding 能力的训练框架。

具体技术路线参考 `docs/architecture.md`。架构非一成不变，agent 认为不合理时可向用户反馈，用户决策后修改。

---

## 工作模式

框架搭建完全由 agent 自驱完成。工作流为：

```
规划方案 → 实施方案 → 运行代码/指令 → 获取执行反馈 → 更新 STATUS.md → 根据结果决定下一步
```

每次执行任务尽量多迭代循环，只有在遇到**必须的决策点**或**无法独立解决的环境问题**时才向用户反馈。

### 状态外化规则

每次取得阶段性进展或即将调用 `/notify` 时，必须将当前状态写入 `STATUS.md`（格式见该文件）。
此文件是 agent 跨会话恢复状态的唯一锚点，每次新会话开始时优先读取，不依赖上下文记忆。

---

## 向用户反馈的方式

在 `.claude/commands/notify.md` 中定义了 `/notify` skill，可自定义消息内容发送给用户。
发送后停止当前任务，等待用户上线给出新的指示。

**每次调用 `/notify` 时，消息必须包含：**
1. 本次通知的主要内容（进展 / 问题 / 决策点）
2. 当前 context 水位评估（低 / 中 / 高），若为"高"则提示用户考虑执行 `/compact` 或 `/clear`

### 何时向用户反馈

1. 遇到网络 / 环境问题，agent 无法独立解决
2. 遇到重大决策点（架构选择、方向调整）
3. 取得阶段性 milestone 进展
4. context 水位评估为"高"时，建议用户执行 `/compact`

---

## 代码仓库

### 本地代码路径
> ✏️ **[在此填写本地代码目录]**
>
> 示例：`project/src/`

### 远端仓库
> ✏️ **[在此填写远端 Git 地址]**
>
> 示例：`https://github.com/your-username/your-repo.git`

每次取得 milestone 时，将代码 commit 并 push，然后通过 `/notify` 通知用户。
代码空间可认为无限，可随意创建与删除分支。

---

## 运行环境

建议为项目创建隔离的虚拟环境，命名规则：`<project-name>-env`。

> ✏️ **[在此填写环境依赖和激活方式]**
>
> 示例：
> ```bash
> python -m venv myproject-env
> source myproject-env/bin/activate
> pip install -r requirements.txt
> ```

---

## 网络环境

> ✏️ **[在此描述网络约束]**
>
> 若处于隔离内网：
> - **禁止**在代码中引用任何外部 URL（CDN、PyPI 官方源、GitHub 等）
> - **pip install 必须加镜像源参数**
> - 可视化必须使用本地已安装的库（如 plotly，用 `include_plotlyjs=True` 内嵌 JS）
>
> 示例镜像源用法：
> ```bash
> pip install <package> -i https://your-internal-mirror/simple
> ```

---

## 运行规则

1. 每次尽可能完成更多任务，退出运行状态时**必须**使用 `/notify` 通知用户
2. 优先将进展 / 现状 / 结论写入 `STATUS.md`，以文件存储换取昂贵的 context 空间
3. `/compact` 和 `/clear` 是 CLI 内部命令，agent 无法自主调用，只能由用户手动执行。agent 的职责是：在每次 `/notify` 消息中报告 context 水位，由用户决定是否压缩
4. 上下文恢复：见文档顶部"上下文恢复协议"章节

---

## 工程规范

### 文件结构（必须遵守，禁止随意散落文件）

```
project/
├── 文档（根目录）
│   ├── CLAUDE.md          ← Agent 执行手册（本文件）
│   ├── STATUS.md          ← 进度快照（Agent 维护）
│   ├── DEVLOG.md          ← 开发日志
│   ├── docs/
│   │   └── architecture.md ← 架构设计
│   └── README.md          ← 项目概览
│
├── src/                   ← 核心代码
│
├── tools/                 ← 所有工具脚本放这里
│
├── evals/                 ← 所有评测产出放这里（JSON、HTML、日志）
│
└── runs/                  ← 训练 / 实验产出（勿提交大文件）
    └── run_YYYYMMDD_<RUN_DESC>/
        ├── README.md      ← run 目的与配置（自动生成）
        └── ...
```

**规则：**
- 新增工具脚本 → `tools/`
- 评测结果 / 可视化 → `evals/`
- 实验产出 → `runs/run_*/`
- **禁止**在根目录散落 `.json`、`.html`、`.log` 等产出文件

### 实验启动规范

每次启动实验时，**必须**通过 `RUN_DESC` 环境变量指定本次 run 的描述：

```bash
# 格式：RUN_DESC="<描述>" python src/train.py
RUN_DESC="exp_baseline_lr1e4_10ep" python src/train.py
```

- 描述应概括：模型规格 / 数据集 / 关键超参 / 实验目的
- 最终目录名为 `run_YYYYMMDD_<描述>`
- 启动后自动在 run 目录内创建 `README.md`，记录描述、配置和文件说明

### 产出文件路径

所有实验产出必须落在项目目录内，**严禁**写入 `/tmp` 或其他外部目录。
