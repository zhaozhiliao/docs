# AI 配置详解

详细介绍如何配置和使用 QuickNote 的 AI 功能。

## 🤖 支持的 AI 服务

QuickNote 支持三种 AI 提供商：

| 提供商 | 优势 | 适用场景 |
|--------|------|----------|
| OpenAI | 通用性强，生态完善 | 日常总结、任务提取 |
| Anthropic | 长文本处理优秀 | 深度分析、长笔记 |
| 自定义 | 灵活性高，可自建 | 特殊需求、私有部署 |

## 🔑 OpenAI 配置

### 获取 API Key

1. 访问 https://platform.openai.com/
2. 注册/登录账号
3. 点击右上角头像 → **API keys**
4. 点击 **Create new secret key**
5. 输入名称（如"QuickNote"）
6. 复制密钥（只显示一次）

### 在 QuickNote 中配置

1. 打开 QuickNote 设置
2. 切换到 **AI** 标签页
3. 选择提供商：**OpenAI**
4. 粘贴 API Key
5. 选择模型

### 推荐模型

| 模型 | 特点 | 价格 | 适用场景 |
|------|------|------|----------|
| gpt-4 | 最强大 | 高 | 重要总结 |
| gpt-4-turbo | 性价比高 | 中 | 日常使用（推荐） |
| gpt-3.5-turbo | 快速便宜 | 低 | 简单总结 |

### 自定义 API 地址

如果使用代理或第三方服务：

```
默认: https://api.openai.com/v1
代理: https://your-proxy.com/v1
```

## 🎭 Anthropic 配置

### 获取 API Key

1. 访问 https://console.anthropic.com/
2. 注册/登录账号
3. 进入 **API Keys** 页面
4. 点击 **Create Key**
5. 复制密钥

### 在 QuickNote 中配置

1. 打开设置 → AI
2. 选择提供商：**Anthropic**
3. 粘贴 API Key
4. 选择模型

### 推荐模型

| 模型 | 特点 | 上下文 | 适用场景 |
|------|------|--------|----------|
| claude-3-opus | 最强大 | 200K | 深度分析 |
| claude-3-sonnet | 平衡 | 200K | 日常使用（推荐） |
| claude-3-haiku | 快速 | 200K | 简单任务 |

## 🔧 自定义 API 配置

### 兼容性要求

自定义 API 必须兼容 OpenAI API 格式：

```bash
POST /v1/chat/completions
Content-Type: application/json
Authorization: Bearer YOUR_API_KEY

{
  "model": "your-model",
  "messages": [
    {"role": "system", "content": "..."},
    {"role": "user", "content": "..."}
  ]
}
```

### 配置步骤

1. 打开设置 → AI
2. 选择提供商：**自定义**
3. 输入 API 地址（完整 URL）
4. 输入 API Key
5. 输入模型名称

### 常见自定义服务

**本地部署:**
- Ollama: `http://localhost:11434/v1`
- LM Studio: `http://localhost:1234/v1`
- LocalAI: `http://localhost:8080/v1`

**第三方服务:**
- OpenRouter: `https://openrouter.ai/api/v1`
- Together AI: `https://api.together.xyz/v1`

## 💰 成本优化

### 估算成本

**OpenAI (gpt-4-turbo):**
- 输入: $10/1M tokens
- 输出: $30/1M tokens
- 每次总结约 $0.01-0.05

**Anthropic (claude-3-sonnet):**
- 输入: $3/1M tokens
- 输出: $15/1M tokens
- 每次总结约 $0.005-0.02

### 省钱技巧

1. **选择合适的模型**
   - 日常使用 gpt-3.5-turbo
   - 重要内容用 gpt-4

2. **优化 Prompt**
   - 减少系统提示词长度
   - 避免重复内容

3. **控制频率**
   - 减少自动总结频率
   - 手动触发重要总结

4. **使用本地模型**
   - Ollama + Llama 3
   - 完全免费

## 🔒 安全建议

### API Key 保护

- ✅ 不要分享给他人
- ✅ 定期轮换密钥
- ✅ 使用独立密钥
- ✅ 设置使用限额

### 权限控制

OpenAI 支持设置权限：
1. 访问 API Keys 页面
2. 编辑密钥权限
3. 限制模型访问
4. 设置月度预算

## 🧪 测试配置

### 快速测试

配置完成后测试：

1. 记录 2-3 条测试笔记
2. 手动生成总结
3. 检查是否成功

### 命令行测试

**OpenAI:**
```bash
curl https://api.openai.com/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_KEY" \
  -d '{
    "model": "gpt-3.5-turbo",
    "messages": [{"role": "user", "content": "测试"}]
  }'
```

**Anthropic:**
```bash
curl https://api.anthropic.com/v1/messages \
  -H "content-type: application/json" \
  -H "x-api-key: YOUR_KEY" \
  -H "anthropic-version: 2023-06-01" \
  -d '{
    "model": "claude-3-sonnet-20240229",
    "max_tokens": 1024,
    "messages": [{"role": "user", "content": "测试"}]
  }'
```

## ❓ 常见问题

**Q: API Key 无效？**
- 检查是否复制完整
- 确认密钥未过期
- 验证账号有余额

**Q: 调用失败？**
- 检查网络连接
- 验证 API 地址正确
- 查看错误信息

**Q: 响应太慢？**
- 尝试更快的模型
- 检查网络延迟
- 考虑使用代理

## 🎯 下一步

- 📝 优化 [Prompt 模板](prompt-optimization.md)
- 🌐 配置 [Webhook 集成](webhook-guide.md)
- 💡 查看 [最佳实践](best-practices.md)
