# 工作流导入与交接指南

## 适用对象

这份指南面向需要接手工作流的内容运营、产品运营或自动化维护人员。无需从头理解所有代码，但需要具备基本的 n8n 节点、凭证和执行记录概念。

## 导入步骤

1. 在 n8n 选择 **Import from File**。
2. 导入所需的 `*.redacted.json`。
3. 保持 Schedule Trigger 关闭。
4. 按节点提示重新连接 credentials。
5. 搜索 `YOUR_` 与 `REDACTED_`，确保所有占位符均已替换。
6. 使用一条测试数据执行，检查每个节点输入和输出。
7. 确认 WordPress 状态为 `draft` 后再进行完整测试。
8. 验证异常路径和空数据路径，再启用定时任务。

## 所需凭证

不同工作流可能需要：

- PubMed / NCBI API key
- DeepSeek / OpenRouter / xAI
- Firecrawl
- WordPress REST API 或 Application Password
- PostgreSQL
- SiliconFlow embedding API
- 飞书机器人 Webhook
- Google Sheets OAuth

## 日常检查

- Schedule Trigger 是否按时启动
- HTTP 节点是否出现 401、403、429 或 5xx
- 模型输出是否仍为可解析 JSON
- PubMed 是否有异常重复 PMID
- WordPress 是否产生异常短标题或正文
- PGVector embedding 是否保持 1024 维
- 飞书卡片是否保留原始链接

## 常见故障

### API 401 / 403

检查 credential 是否过期，或请求头是否仍使用示例占位符。

### 模型 JSON 解析失败

查看原始输出是否包含 Markdown 代码块、额外解释或被截断。必要时缩短输入并重试。

### 没有新文章

先检查时间窗口和关键词，不要直接判断工作流故障。PubMed、RSS 和 X 在某些时段可能没有符合门槛的新内容。

### 出现重复文章

检查 PMID static data、WordPress slug 和数据库 upsert matching columns。

### 工作流能运行但没有下游输出

逐个检查 IF、阈值过滤和空数组处理节点，确认是业务规则过滤还是节点异常。

## 变更原则

- 修改阈值、关键词或模型 Prompt 时保留版本记录
- 不在 Code 节点或 URL 中硬编码 API key
- 新增来源时优先独立验证，再并入主流程
- 自动发布前保留草稿模式的回滚开关
