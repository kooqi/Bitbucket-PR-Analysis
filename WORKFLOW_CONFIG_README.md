# N8N Bitbucket PR 分析工作流配置说明

## 文件说明
- `bitbucket_pr_analysis_workflow.json` - N8N workflow 配置文件

## 工作流概述

这个 N8N workflow 实现了以下功能：
1. ⏰ 每天晚上 22:00 自动触发
2. 📥 从 Bitbucket 获取当天创建的 Pull Requests
3. 🤖 使用千问大模型对代码进行多维度评估
4. 📊 按高、中、低三个级别分类问题
5. 📤 将结果输出到飞书多维表格

## 导入步骤

1. 登录你的 N8N 实例
2. 点击右上角的"Import from File"或"Import from URL"
3. 选择 `bitbucket_pr_analysis_workflow.json` 文件
4. 导入成功后配置所需的凭据

## 必需的配置项

### 1. 环境变量设置

在 N8N 中设置以下环境变量（Settings → Variables）：

```bash
# Bitbucket 配置
BITBUCKET_WORKSPACE=your_workspace_name
BITBUCKET_REPO=your_repository_name

# 飞书多维表格配置
FEISHU_APP_TOKEN=your_feishu_app_token
FEISHU_TABLE_ID=your_feishu_table_id
```

### 2. 凭据配置

#### Bitbucket 凭据 (HTTP Basic Auth)
- **Credential Name**: `Bitbucket Credentials`
- **Type**: Basic Auth
- **Username**: 你的 Bitbucket 用户名或 App Password 用户名
- **Password**: Bitbucket App Password

创建 Bitbucket App Password:
1. 登录 Bitbucket
2. 进入 Settings → Personal settings → App passwords
3. 创建新的 App Password，权限需要包含: `pullrequest:read`, `repository:read`

#### 千问 API 凭据 (HTTP Header Auth)
- **Credential Name**: `Qianwen API Key`
- **Type**: Header Auth
- **Name**: `Authorization`
- **Value**: `Bearer YOUR_QIANWEN_API_KEY`

获取千问 API Key:
1. 访问阿里云 DashScope: https://dashscope.console.aliyun.com/
2. 创建 API Key
3. 格式: `Bearer sk-xxxxxxxxxxxxx`

#### 飞书凭据 (HTTP Header Auth)
- **Credential Name**: `Feishu Access Token`
- **Type**: Header Auth
- **Name**: `Authorization`
- **Value**: `Bearer YOUR_FEISHU_ACCESS_TOKEN`

获取飞书 Access Token:
1. 创建飞书自建应用: https://open.feishu.cn/app
2. 添加权限: `bitable:app`, `bitable:table:write`
3. 获取 tenant_access_token (注意定期刷新)

### 3. 飞书多维表格字段配置

在飞书多维表格中创建以下字段：

| 字段名称 | 字段类型 | 说明 |
|---------|---------|------|
| PR ID | 文本 | Pull Request ID |
| PR 标题 | 文本 | PR 标题 |
| 提交者 | 文本 | PR 作者 |
| PR 链接 | URL | PR 的链接 |
| 评估日期 | 日期时间 | 评估时间 |
| 问题级别 | 单选 | 高/中/低/无 |
| 问题类别 | 单选 | 代码缺陷/注释程度/合理性/安全性 |
| 问题描述 | 多行文本 | 问题详细描述 |
| 代码位置 | 文本 | 问题所在位置 |
| 改进建议 | 多行文本 | 改进建议 |
| 总体评分 | 文本 | 0-100 分 |
| 总结 | 多行文本 | 总体评价 |

## 节点说明

### 1. Schedule Trigger (定时触发器)
- 每天 22:00 自动触发
- Cron 表达式: `0 22 * * *`

### 2. Bitbucket Get PRs (获取 PRs)
- 调用 Bitbucket API 获取当天创建的 PRs
- 过滤条件: `created_on >= 今天`
- 状态: `OPEN`

### 3. Parse PRs (解析 PR 数据)
- 提取 PR 的基本信息
- 准备后续处理所需的数据

### 4. Get PR Diff (获取代码变更)
- 获取每个 PR 的代码差异
- 为 AI 分析提供代码内容

### 5. Prepare AI Prompt (准备 AI 提示词)
- 构造发送给千问的提示词
- 包含 PR 信息和代码 diff
- 限制 diff 长度避免 token 超限

### 6. Call Qianwen API (调用千问 API)
- 使用千问大模型进行代码评估
- 评估维度: 代码缺陷、注释程度、合理性、安全性
- 返回结构化的评估结果

### 7. Categorize Issues (问题分类)
- 解析 AI 返回的评估结果
- 按高、中、低三个级别分类问题
- 统计各级别问题数量

### 8. Format for Feishu (格式化飞书输出)
- 将评估结果格式化为飞书表格行
- 每个问题一行
- 关联提交者信息

### 9. Write to Feishu Table (写入飞书表格)
- 批量写入飞书多维表格
- 每个问题作为独立记录

## 执行顺序

```
1. Schedule Trigger (22:00 触发)
   ↓
2. Bitbucket Get PRs (获取 PRs)
   ↓
3. Parse PRs (解析数据)
   ↓
4. Get PR Diff (获取代码变更) - 循环处理每个 PR
   ↓
5. Prepare AI Prompt (准备提示词)
   ↓
6. Call Qianwen API (AI 评估)
   ↓
7. Categorize Issues (问题分类)
   ↓
8. Format for Feishu (格式化输出)
   ↓
9. Write to Feishu Table (写入表格)
```

## 测试步骤

1. **手动触发测试**
   - 在 N8N 中打开 workflow
   - 点击 "Execute Workflow" 按钮
   - 检查每个节点的输出

2. **检查 Bitbucket 连接**
   - 验证 Bitbucket API 是否返回 PR 列表
   - 确认权限配置正确

3. **检查千问 API**
   - 验证 API Key 是否有效
   - 检查返回的评估结果格式

4. **检查飞书写入**
   - 确认表格字段名称匹配
   - 验证数据是否正确写入

## 注意事项

1. **API 限流**: Bitbucket、千问和飞书 API 都有限流，请注意调用频率
2. **Token 限制**: 千问 API 对输入 token 有限制，代码 diff 会被截断至 8000 字符
3. **飞书 Token**: 飞书的 access_token 需要定期刷新（2小时有效期）
4. **数据量**: 如果 PR 数量很多，执行时间会较长
5. **错误处理**: 建议在 workflow 设置中启用错误通知

## 优化建议

1. **增加错误处理节点**: 捕获和记录错误信息
2. **添加条件分支**: 根据 PR 大小决定是否进行分析
3. **批量处理优化**: 对多个 PR 进行批量处理而非逐个处理
4. **缓存机制**: 避免重复分析相同的 PR
5. **通知机制**: 完成后发送飞书通知

## 故障排查

### 问题 1: Bitbucket API 返回 401
- 检查 Bitbucket 凭据配置
- 确认 App Password 权限

### 问题 2: 千问 API 调用失败
- 检查 API Key 是否正确
- 确认账户余额是否充足

### 问题 3: 飞书写入失败
- 检查 access_token 是否过期
- 确认表格字段名称是否匹配
- 验证应用权限配置

### 问题 4: 没有获取到 PR
- 检查时间过滤条件
- 确认 repository 和 workspace 名称正确
- 验证当天是否有新创建的 PR

## 版本信息

- N8N 版本: 1.115.3
- 创建日期: 2025-10-18
- 维护者: 请根据实际情况填写

## 许可证

根据项目需求添加相应的许可证信息。
