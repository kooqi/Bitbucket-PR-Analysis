# 快速开始指南

## 5 分钟快速部署 N8N Bitbucket PR 分析工作流

### 步骤 1: 导入 Workflow 到 N8N

1. 打开你的 N8N 实例
2. 点击右上角的 "+" → "Import from File"
3. 选择 `bitbucket_pr_analysis_workflow.json`
4. 点击导入

### 步骤 2: 配置环境变量

在 N8N 中设置环境变量（Settings → Variables）：

```bash
BITBUCKET_WORKSPACE=my-workspace
BITBUCKET_REPO=my-repo
FEISHU_APP_TOKEN=bascnxxxxx
FEISHU_TABLE_ID=tblxxxxx
```

### 步骤 3: 配置凭据

#### 3.1 Bitbucket 凭据

1. 在 N8N 中创建新凭据：Credentials → Add Credential → Basic Auth
2. 命名为：`Bitbucket Credentials`
3. 填入：
   - Username: 你的 Bitbucket 用户名
   - Password: Bitbucket App Password（在 Bitbucket Settings → App passwords 创建）

#### 3.2 千问 API 凭据

1. 在 N8N 中创建新凭据：Credentials → Add Credential → Header Auth
2. 命名为：`Qianwen API Key`
3. 填入：
   - Name: `Authorization`
   - Value: `Bearer sk-your-qianwen-api-key`

#### 3.3 飞书凭据

1. 在 N8N 中创建新凭据：Credentials → Add Credential → Header Auth
2. 命名为：`Feishu Access Token`
3. 填入：
   - Name: `Authorization`
   - Value: `Bearer t-your-feishu-access-token`

### 步骤 4: 创建飞书多维表格

1. 在飞书中创建一个新的多维表格
2. 添加以下字段（按顺序）：

| 字段名 | 类型 |
|-------|------|
| PR ID | 文本 |
| PR 标题 | 文本 |
| 提交者 | 文本 |
| PR 链接 | URL |
| 评估日期 | 日期时间 |
| 问题级别 | 单选（高/中/低/无）|
| 问题类别 | 单选（代码缺陷/注释程度/合理性/安全性）|
| 问题描述 | 多行文本 |
| 代码位置 | 文本 |
| 改进建议 | 多行文本 |
| 总体评分 | 文本 |
| 总结 | 多行文本 |

3. 从 URL 中获取 `app_token` 和 `table_id`：
   ```
   https://xxx.feishu.cn/base/{app_token}?table={table_id}
   ```

### 步骤 5: 测试 Workflow

1. 在 N8N 中打开导入的 workflow
2. 点击右上角的 "Execute Workflow" 按钮
3. 检查每个节点的执行结果
4. 确认数据已写入飞书表格

### 步骤 6: 激活定时任务

1. 在 workflow 页面，点击右上角的 "Active" 开关
2. Workflow 将在每天 22:00 自动运行

## 常见问题

### Q1: Bitbucket API 返回 401 错误
**A:** 检查 App Password 权限是否包含 `pullrequest:read` 和 `repository:read`

### Q2: 千问 API 调用失败
**A:** 
- 检查 API Key 格式是否为 `Bearer sk-xxxxx`
- 确认阿里云账户余额充足
- 验证 API Key 是否已启用

### Q3: 飞书写入失败
**A:** 
- 检查 access_token 是否过期（有效期 2 小时）
- 确认表格字段名称与代码中完全一致（包括大小写和空格）
- 验证应用权限是否正确配置

### Q4: 没有获取到 PR
**A:** 
- 检查 `BITBUCKET_WORKSPACE` 和 `BITBUCKET_REPO` 是否正确
- 确认今天是否有新创建的 PR
- 查看 "Bitbucket Get PRs" 节点的输出

### Q5: 代码 diff 太大导致超时
**A:** 
- 在 "Prepare AI Prompt" 节点中调整 `maxDiffLength` 参数
- 考虑只分析关键文件的变更

## 工作流节点详解

```
[Schedule Trigger] → 每天 22:00 触发
         ↓
[Bitbucket Get PRs] → 获取今日创建的 PRs
         ↓
[Parse PRs] → 提取 PR 基本信息
         ↓
[Get PR Diff] → 获取代码变更（循环处理每个 PR）
         ↓
[Prepare AI Prompt] → 构造 AI 评估提示词
         ↓
[Call Qianwen API] → 调用千问进行代码评估
         ↓
[Categorize Issues] → 分析结果并按级别分类
         ↓
[Format for Feishu] → 格式化为飞书表格行
         ↓
[Write to Feishu Table] → 写入飞书多维表格
```

## 进阶配置

### 自定义评估维度

编辑 "Prepare AI Prompt" 节点，修改 prompt 中的评估维度：

```javascript
请从以下维度进行评估：
1. **代码缺陷**：逻辑错误、边界条件
2. **注释程度**：注释充分性、清晰度
3. **合理性**：设计合理性、最佳实践
4. **安全性**：安全漏洞、敏感信息
5. **性能**：性能优化、资源使用 // 新增维度
6. **可维护性**：代码可读性、结构清晰度 // 新增维度
```

### 添加分支过滤

修改 "Bitbucket Get PRs" 节点的 query 参数：

```javascript
"q": "created_on >= {{ $now.minus({days: 1}).toFormat('yyyy-MM-dd') }} AND source.branch.name=\"feature/*\""
```

### 添加通知功能

在 workflow 末尾添加飞书机器人通知节点：

1. 添加 HTTP Request 节点
2. 配置飞书 Webhook URL
3. 发送完成通知和统计信息

## 监控和维护

### 执行日志
- 在 N8N 中查看：Executions → 选择对应的执行记录
- 查看每个节点的输入输出
- 检查错误信息

### 定期维护
1. **每周**：检查 workflow 执行成功率
2. **每月**：更新飞书 access_token（如果使用手动 token）
3. **每季度**：审查 AI 评估质量，优化 prompt

## 获取帮助

如果遇到问题：
1. 查看 `WORKFLOW_CONFIG_README.md` 详细文档
2. 检查 `credentials_setup_guide.json` 凭据配置
3. 参考 `.env.example` 环境变量示例
4. 查看 N8N 官方文档：https://docs.n8n.io

## 预期输出示例

飞书表格中的数据示例：

| PR ID | PR 标题 | 提交者 | 问题级别 | 问题类别 | 问题描述 | 总体评分 |
|-------|---------|--------|----------|----------|----------|----------|
| 123 | 添加用户认证功能 | 张三 | 高 | 安全性 | 密码未加密存储 | 65 |
| 123 | 添加用户认证功能 | 张三 | 中 | 注释程度 | 关键函数缺少注释 | 65 |
| 124 | 修复登录 bug | 李四 | 低 | 代码缺陷 | 边界条件检查不充分 | 85 |

---

🎉 恭喜！你已经成功部署了 Bitbucket PR 自动分析工作流！
