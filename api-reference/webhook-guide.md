# Webhook 集成指南

详细介绍如何使用 Webhook 将 QuickNote 与其他服务集成。

## 🌐 什么是 Webhook

Webhook 允许 QuickNote 将总结内容自动发送到其他服务，实现无缝集成。

**应用场景:**
- 📝 同步到 Notion 数据库
- 💬 发送到 Slack 频道
- 📋 创建 GitHub Issues
- 📧 发送邮件通知
- 🔄 触发自动化流程

## 🚀 快速开始

### 基本配置

1. 打开设置 → **Webhook** 标签页
2. 点击"添加 Webhook"
3. 填写基本信息:
   - 名称: 标识这个 Webhook
   - URL: 接收数据的地址
   - 方法: POST/PUT/PATCH
4. 配置请求模板
5. 保存并测试

### 发送模式

**全文发送:**
- 将完整总结发送到所有 Webhook
- 适合备份、存档

**AI 智能分析:**
- AI 分析内容并智能路由
- 每个 Webhook 只接收相关内容
- 需要配置 AI 服务

## 📝 Notion 集成

### 创建 Notion Integration

1. 访问 https://www.notion.so/my-integrations
2. 点击"New integration"
3. 填写名称（如"QuickNote"）
4. 选择关联的 workspace
5. 复制 Internal Integration Token

### 获取数据库 ID

1. 打开目标 Notion 数据库
2. 点击右上角"..."→"Copy link"
3. 链接格式: `https://notion.so/workspace/DATABASE_ID?v=...`
4. 提取 DATABASE_ID

### 配置 QuickNote

```json
{
  "name": "Notion 数据库",
  "url": "https://api.notion.com/v1/pages",
  "method": "POST",
  "headers": {
    "Authorization": "Bearer YOUR_INTEGRATION_TOKEN",
    "Notion-Version": "2022-06-28",
    "Content-Type": "application/json"
  },
  "body": {
    "parent": {
      "database_id": "YOUR_DATABASE_ID"
    },
    "properties": {
      "标题": {
        "title": [
          {
            "text": {
              "content": "{{title}}"
            }
          }
        ]
      },
      "日期": {
        "date": {
          "start": "{{date}}"
        }
      },
      "笔记数": {
        "number": {{noteCount}}
      }
    },
    "children": [
      {
        "object": "block",
        "type": "paragraph",
        "paragraph": {
          "rich_text": [
            {
              "text": {
                "content": "{{content}}"
              }
            }
          ]
        }
      }
    ]
  }
}
```

## 💬 Slack 集成

### 创建 Incoming Webhook

1. 访问 https://api.slack.com/apps
2. 创建新应用或选择现有应用
3. 启用 "Incoming Webhooks"
4. 添加 Webhook URL
5. 选择目标频道
6. 复制 Webhook URL

### 配置 QuickNote

```json
{
  "name": "Slack 通知",
  "url": "YOUR_SLACK_WEBHOOK_URL",
  "method": "POST",
  "headers": {
    "Content-Type": "application/json"
  },
  "body": {
    "text": "📝 {{title}}",
    "blocks": [
      {
        "type": "header",
        "text": {
          "type": "plain_text",
          "text": "{{title}}"
        }
      },
      {
        "type": "section",
        "text": {
          "type": "mrkdwn",
          "text": "{{content}}"
        }
      },
      {
        "type": "context",
        "elements": [
          {
            "type": "mrkdwn",
            "text": "📊 {{noteCount}} 条笔记 | {{wordCount}} 字"
          }
        ]
      }
    ]
  }
}
```

## 🐙 GitHub Issues 集成

### 创建 Personal Access Token

1. GitHub Settings → Developer settings
2. Personal access tokens → Tokens (classic)
3. Generate new token
4. 勾选 `repo` 权限
5. 复制 token

### 配置 QuickNote

```json
{
  "name": "GitHub Issues",
  "url": "https://api.github.com/repos/USERNAME/REPO/issues",
  "method": "POST",
  "headers": {
    "Authorization": "Bearer YOUR_TOKEN",
    "Accept": "application/vnd.github+json"
  },
  "body": {
    "title": "{{title}}",
    "body": "{{content}}",
    "labels": ["quicknote", "summary"]
  }
}
```

## 🤖 AI 智能路由

### 工作原理

1. AI 分析总结内容
2. 根据 Webhook 用途描述判断相关性
3. 为每个 Webhook 提取相关内容
4. 分别发送

### 配置用途描述

**好的描述:**
```
工作 Webhook:
"保存工作相关的任务、会议记录、项目进展和团队协作内容"

学习 Webhook:
"记录技术学习笔记、代码片段、文章链接和知识点总结"

个人 Webhook:
"收集个人想法、生活记录、灵感和创意"
```

**不好的描述:**
```
"Notion"  # 太模糊
"备份"    # 不明确
"数据"    # 无意义
```

### 自定义路由规则

在"AI 路由自定义规则"中添加:

```markdown
## 分类规则
1. 包含"会议"、"任务"、"项目"的内容 → 工作 Webhook
2. 包含"学习"、"代码"、"技术"的内容 → 学习 Webhook
3. 包含"想法"、"灵感"、"创意"的内容 → 个人 Webhook

## 特殊处理
- 标记为"紧急"的内容优先发送
- 代码片段必须发送到技术 Webhook
- 待办事项发送到任务管理 Webhook

## 排除规则
- 不发送测试内容
- 不发送草稿标记的内容
```

## 🔧 高级配置

### 请求头

常用请求头:

```json
{
  "Content-Type": "application/json",
  "Authorization": "Bearer TOKEN",
  "User-Agent": "QuickNote/1.0",
  "X-Custom-Header": "value"
}
```

### 请求体模板

支持的变量:

| 变量 | 类型 | 说明 |
|------|------|------|
| `{{title}}` | 字符串 | 总结标题 |
| `{{content}}` | 字符串 | 总结内容 |
| `{{date}}` | 字符串 | 日期 (YYYY-MM-DD) |
| `{{noteCount}}` | 数字 | 笔记数量 |
| `{{wordCount}}` | 数字 | 总字数 |

### 条件发送

使用 AI 智能路由实现条件发送:

```markdown
## 条件规则
- 只有笔记数 > 5 才发送到主 Webhook
- 包含代码的内容发送到技术 Webhook
- 包含链接的内容发送到资源 Webhook
```

## 🧪 测试和调试

### 测试 Webhook

1. 配置完成后点击"测试"
2. QuickNote 发送测试数据
3. 检查接收端是否收到
4. 查看响应状态

### 查看日志

1. 设置 → 统计
2. 查看 Webhook 统计
3. 检查成功/失败次数
4. 查看错误信息

### 常见错误

| 错误 | 原因 | 解决方法 |
|------|------|----------|
| 401 Unauthorized | 认证失败 | 检查 Token |
| 404 Not Found | URL 错误 | 验证 URL |
| 400 Bad Request | 格式错误 | 检查 JSON |
| 500 Server Error | 服务端问题 | 联系服务提供商 |

## 💡 最佳实践

### Webhook 数量

- **推荐**: 2-5 个
- **最多**: 10 个
- 过多会影响性能

### 命名规范

使用清晰的名称:
- ✅ "Notion 工作数据库"
- ✅ "Slack 团队频道"
- ❌ "Webhook 1"
- ❌ "测试"

### 错误处理

- 配置重试机制
- 监控失败率
- 及时处理错误

### 安全建议

- 使用 HTTPS
- 保护 API Token
- 限制权限范围
- 定期轮换密钥

## 🎯 实用示例

### 邮件通知

使用 Mailgun/SendGrid:

```json
{
  "name": "邮件通知",
  "url": "https://api.mailgun.net/v3/YOUR_DOMAIN/messages",
  "method": "POST",
  "headers": {
    "Authorization": "Basic YOUR_API_KEY"
  },
  "body": {
    "from": "QuickNote <noreply@yourdomain.com>",
    "to": "your@email.com",
    "subject": "{{title}}",
    "text": "{{content}}"
  }
}
```

### Telegram 通知

```json
{
  "name": "Telegram Bot",
  "url": "https://api.telegram.org/botYOUR_BOT_TOKEN/sendMessage",
  "method": "POST",
  "body": {
    "chat_id": "YOUR_CHAT_ID",
    "text": "📝 {{title}}\n\n{{content}}",
    "parse_mode": "Markdown"
  }
}
```

### 自定义 API

```json
{
  "name": "自定义服务",
  "url": "https://your-api.com/webhook",
  "method": "POST",
  "headers": {
    "X-API-Key": "YOUR_KEY"
  },
  "body": {
    "type": "summary",
    "data": {
      "title": "{{title}}",
      "content": "{{content}}",
      "metadata": {
        "date": "{{date}}",
        "stats": {
          "notes": {{noteCount}},
          "words": {{wordCount}}
        }
      }
    }
  }
}
```

## 🔗 更多资源

- [Notion API 文档](https://developers.notion.com/)
- [Slack API 文档](https://api.slack.com/)
- [GitHub API 文档](https://docs.github.com/rest)
- [Webhook 最佳实践](https://webhooks.fyi/)

## 🆘 获取帮助

遇到问题？
- 查看 [常见问题](faq.md)
- 访问 [GitHub Issues](https://github.com/zhaozhiliao/QuickNote/issues)
