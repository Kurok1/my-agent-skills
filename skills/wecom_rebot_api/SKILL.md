---
name: wecom_rebot_api
version: v1.0
description: 使用企业微信机器人api，发送企业微信群聊通知
---

## 1) 能力描述（What）

从用户对话中获取企业微信群聊机器人的 webhook key（形如：xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx）

调用企业微信机器人 API：https://qyapi.weixin.qq.com/cgi-bin/webhook/send?key=KEY

支持发送：

* text（纯文本）

* markdown（企业微信支持的 Markdown 语法子集）

## 2) 触发条件（When）

**用户说“发到企业微信/发到群机器人/用企业微信机器人发消息/推送到群”等**

**或用户提供了 key=... / “我的 key 是 ...” 并要求发送内容**

## 3) 输入与参数（Inputs）

* key：由用户在对话中提供（必需，但允许首次缺省后再追问）

* msgtype：text 或 markdown（默认 text）

* content：要发送的内容（必需）

* mentions（可选，仅 text 常用）：

* mentioned_list：要 @ 的成员 userid 列表（可选）

* mentioned_mobile_list：要 @ 的手机号列表（可含 "@all"）

## 4) 输出（Outputs）

发送成功：返回“已发送”并回显企业微信返回的 errmsg

发送失败：返回可操作的错误解释（常见：key 无效、内容格式不对、频率限制）

## 5) 状态管理（State）

* session.wecom_key：仅在当前会话有效（建议不要持久化到磁盘/数据库）

安全建议：

1. 不在日志中打印完整 key（只保留前后各 3~4 位用于排查）

2. 用户可以随时说“更新 key / 换 key”来覆盖

# 对话流程（推荐）

A. 第一次发送（用户未给 key）

* 助手：请提供企业微信群机器人 webhook 的 key（不是完整 URL 也可以）

* 用户：key 是 xxxx...

* 助手：收到。你要发送的内容是？（可选：让用户选 text/markdown）

* 用户：发送“xxx”

* 助手：调用 API，返回结果

B. 用户直接给 key + 内容

* 用户：用企业微信机器人发消息，key=xxx，内容：今天 18:00 发布

* 助手：解析参数→发送→回结果

C. 更新 key

* 用户：换一个 key=yyy

* 助手：覆盖 session key，并提示“已更新”

# 发送指令

## 发送text
```
KEY='用户提供的key'
CONTENT='这里是要发送的内容'

curl -sS "https://qyapi.weixin.qq.com/cgi-bin/webhook/send?key=${KEY}" \
  -H 'Content-Type: application/json' \
  -d "{\"msgtype\":\"text\",\"text\":{\"content\":\"${CONTENT}\"}}"
```

## 发送markdown
```
KEY='你的key'
MD='**标题**\n> 引用内容\n- 列表1\n- 列表2'

curl -sS "https://qyapi.weixin.qq.com/cgi-bin/webhook/send?key=${KEY}" \
  -H 'Content-Type: application/json' \
  -d "{\"msgtype\":\"markdown\",\"markdown\":{\"content\":\"${MD}\"}}"
```

