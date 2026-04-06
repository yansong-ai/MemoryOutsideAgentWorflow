# 项目状态文件 — Agent 跨会话恢复锚点

> 此文件由 agent 在每次 milestone 或调用 /notify 前更新。
> 新会话开始时，agent 必须优先读取此文件恢复状态，不依赖上下文记忆。

---

## 最后更新时间
<!-- 格式：YYYY-MM-DD HH:MM，每次更新必填 -->
YYYY-MM-DD HH:MM

## 当前阶段
<!-- 简短描述当前所处阶段，例如："Milestone 3：数据管道搭建完成" -->
**Milestone 0：项目初始化**

## 已完成的工作
<!-- 用 checkbox 列表，已完成的打勾 -->
- [ ] 初始化项目结构
- [ ] 填写 CLAUDE.md 项目特定配置
- [ ] 填写 docs/architecture.md 技术路线

## 下一步
<!-- 按优先级列出，最多 3-5 条，agent 恢复时优先执行第 1 条 -->
1. 【在此填写第一个具体任务】
2. 【在此填写第二个具体任务】

## 阻塞项
<!-- 列出当前阻塞原因，无阻塞则写"无" -->
无

## 关键决策记录
| 时间 | 决策内容 | 原因 |
|------|----------|------|
| <!-- YYYY-MM-DD --> | <!-- 决策内容 --> | <!-- 原因 --> |

## 环境信息
<!-- 记录关键路径，方便跨会话恢复 -->
- 虚拟环境: `<!-- /path/to/venv/bin/python -->`
- 激活命令: `source <!-- /path/to/venv/bin/activate -->`
- 代码目录: `<!-- /path/to/project/ -->`
- 远端 Git: `<!-- https://github.com/your-username/your-repo.git -->`

## Context 水位记录
| 时间 | 水位 | 备注 |
|------|------|------|
| <!-- YYYY-MM-DD HH:MM --> | 低 | 项目初始化 |
