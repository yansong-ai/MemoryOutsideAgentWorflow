# 发送通知

向消息机器人发送通知消息。

## 使用方式

`/notify <消息内容>`

如果没有提供参数，发送一条包含当前时间的默认状态通知。

## 配置

在使用前，将下方 `WEBHOOK` 替换为你的实际 Webhook 地址。

支持的平台示例：
- 飞书（Lark）：`https://open.feishu.cn/open-apis/bot/v2/hook/<token>`
- 钉钉：`https://oapi.dingtalk.com/robot/send?access_token=<token>`
- Slack：`https://hooks.slack.com/services/<token>`

## 执行步骤

根据用户提供的参数 `$ARGUMENTS`，通过 Webhook 发送消息。

1. 如果 `$ARGUMENTS` 不为空，将其作为消息正文发送
2. 如果 `$ARGUMENTS` 为空，发送一条包含当前时间的默认通知

运行以下 Bash 命令：

```bash
WEBHOOK="https://your-webhook-url-here"  # ✏️ 替换为你的 Webhook 地址
MSG="${ARGUMENTS:-"Agent 通知：任务已完成（$(date '+%Y-%m-%d %H:%M:%S')）"}"

curl -s -X POST "$WEBHOOK" \
  -H "Content-Type: application/json" \
  -d "{\"msg_type\":\"text\",\"content\":{\"text\":\"$MSG\"}}"
```

执行后，告知用户发送结果（成功 / 失败）。
