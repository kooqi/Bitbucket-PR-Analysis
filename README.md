# N8N Bitbucket PR 自动分析工作流

> 🎯 每天自动分析 Bitbucket PR，使用千问 AI 评估代码质量，将结果输出到飞书多维表格

[![N8N Version](https://img.shields.io/badge/N8N-1.115.3-blue.svg)](https://n8n.io/)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)

## ✨ 功能特性

- ⏰ **自动触发**: 每天 22:00 自动运行
- 📥 **智能获取**: 自动获取当天创建的 Pull Requests
- 🤖 **AI 评估**: 使用千问大模型多维度评估代码质量
  - 代码缺陷检测
  - 注释程度评估
  - 合理性分析
  - 安全性审查
- 📊 **分级管理**: 按高、中、低三个级别自动分类问题
- 👥 **责任到人**: 每个问题关联到具体提交者
- 📤 **结果输出**: List 格式输出到飞书多维表格

---

## 🚀 快速开始

### N8N 开源版用户（推荐）⭐

```bash
1. 导入文件: bitbucket_pr_analysis_workflow_opensource.json
2. 修改节点: "⚙️ 配置中心" 节点中的 4 个配置项
3. 配置凭据: Bitbucket、千问、飞书（3 个）
4. 测试并激活工作流
```

📖 **详细指南**: [QUICKSTART_OPENSOURCE.md](./QUICKSTART_OPENSOURCE.md) ← 从这里开始

### N8N 企业版/Cloud 版用户

```bash
1. 设置变量: Settings → Variables（4 个环境变量）
2. 导入文件: bitbucket_pr_analysis_workflow.json
3. 配置凭据: Bitbucket、千问、飞书（3 个）
4. 测试并激活工作流
```

📖 **详细指南**: [QUICKSTART.md](./QUICKSTART.md)

### 不确定使用哪个版本？

📊 **查看对比**: [VERSION_COMPARISON.md](./VERSION_COMPARISON.md) - 了解版本区别和如何选择

---

## 📦 文件说明

### 核心文件

| 文件名 | 用途 | 适用版本 |
|-------|------|---------|
| **`bitbucket_pr_analysis_workflow_opensource.json`** ⭐ | 工作流配置（开源版） | **所有版本通用** |
| `bitbucket_pr_analysis_workflow.json` | 工作流配置（企业版） | 企业版/Cloud版 |

### 文档文件

| 文件名 | 说明 | 推荐阅读 |
|-------|------|---------|
| **`QUICKSTART_OPENSOURCE.md`** ⭐ | 开源版 5 分钟快速开始 | 开源版用户必读 |
| `QUICKSTART.md` | 企业版快速开始指南 | 企业版用户必读 |
| `VERSION_COMPARISON.md` | 版本对比和选择指南 | 不确定版本时阅读 |
| `WORKFLOW_CONFIG_README.md` | 详细配置文档 | 深入了解配置 |
| `FILES_SUMMARY.md` | 文件清单和架构说明 | 了解项目结构 |
| `credentials_setup_guide.json` | 凭据配置指南（JSON） | 配置凭据时参考 |
| `.env.example` | 环境变量示例 | 仅企业版参考 |

---

## ⚙️ 配置要点

### 开源版配置（在 workflow 中）

双击 **"⚙️ 配置中心 (请修改这里)"** 节点，修改：

```javascript
BITBUCKET_WORKSPACE  →  your_workspace_name
BITBUCKET_REPO       →  your_repository_name
FEISHU_APP_TOKEN     →  bascnxxxxxxxxxxxxxxxxxxxxx
FEISHU_TABLE_ID      →  tblxxxxxxxxxxxxxxxxxxxxx
```

### 企业版配置（在 Settings 中）

进入 **Settings → Variables**，添加：

```bash
BITBUCKET_WORKSPACE=your_workspace_name
BITBUCKET_REPO=your_repository_name
FEISHU_APP_TOKEN=your_feishu_app_token
FEISHU_TABLE_ID=your_feishu_table_id
```

---

## 🔐 凭据配置

需要配置 3 个凭据：

### 1️⃣ Bitbucket Credentials (Basic Auth)

```
Username: 你的 Bitbucket 用户名
Password: Bitbucket App Password
```

📝 **获取方法**: Bitbucket → Settings → App passwords → Create
- 权限: `pullrequest:read`, `repository:read`

### 2️⃣ Qianwen API Key (Header Auth)

```
Header Name: Authorization
Header Value: Bearer sk-your-api-key
```

📝 **获取方法**: https://dashscope.console.aliyun.com/ → API-KEY 管理

### 3️⃣ Feishu Access Token (Header Auth)

```
Header Name: Authorization
Header Value: Bearer t-your-access-token
```

📝 **获取方法**: https://open.feishu.cn/ → 创建应用 → 获取 tenant_access_token

详细步骤: [credentials_setup_guide.json](./credentials_setup_guide.json)

---

## 📋 飞书表格字段

在飞书中创建多维表格，添加以下字段（**字段名必须完全一致**）：

| 字段名 | 类型 | 说明 |
|-------|-----|------|
| **PR ID** | 文本 | Pull Request ID |
| **PR 标题** | 文本 | PR 标题 |
| **提交者** | 文本 | PR 作者 |
| **PR 链接** | URL | PR 链接 |
| **评估日期** | 日期时间 | 评估时间 |
| **问题级别** | 单选 | 高/中/低/无 |
| **问题类别** | 单选 | 代码缺陷/注释程度/合理性/安全性/无问题 |
| **问题描述** | 多行文本 | 问题详细描述 |
| **代码位置** | 文本 | 代码位置 |
| **改进建议** | 多行文本 | 改进建议 |
| **总体评分** | 文本 | 0-100 分 |
| **总结** | 多行文本 | 总体评价 |

---

## 🎯 工作流程

### 开源版流程（10 个节点）

```
1. Schedule Trigger        → 每天 22:00 触发
2. ⚙️ 配置中心 ⭐         → 存储配置（需修改）
3. Bitbucket Get PRs       → 获取当天 PRs
4. Parse PRs               → 解析 PR 数据
5. Get PR Diff             → 获取代码变更（循环）
6. Prepare AI Prompt       → 构造评估提示词
7. Call Qianwen API        → AI 评估
8. Categorize Issues       → 问题分类（高/中/低）
9. Format for Feishu       → 格式化输出
10. Write to Feishu Table  → 写入飞书表格
```

### 企业版流程（9 个节点）

与开源版相同，但使用 **Settings → Variables** 代替配置节点。

---

## ❓ 常见问题

| 问题 | 解决方法 |
|------|---------|
| 🔴 Bitbucket 401 错误 | 检查 App Password 权限 |
| 🔴 千问 API 调用失败 | 检查 API Key 格式：`Bearer sk-xxxxx` |
| 🔴 飞书写入失败 | 确认字段名称完全匹配 |
| 🔴 没有获取到 PR | 确认今天是否有新创建的 PR |
| 🔴 配置节点不存在 | 确认使用 `*_opensource.json` 文件 |

详细排查: [QUICKSTART_OPENSOURCE.md](./QUICKSTART_OPENSOURCE.md#常见问题排查)

---

## 💡 注意事项

- ⏱️ **API 限流**: 注意各 API 的调用频率限制
- 📏 **Token 限制**: 代码 diff 默认截断至 8000 字符
- 🔄 **飞书 Token**: access_token 有效期 2 小时，需定期刷新
- 🔒 **数据安全**: PR 代码会发送给千问 API，请确保符合公司安全政策

---

## 📈 预期效果

使用后你将获得：

✅ 每日自动生成的 PR 质量报告  
✅ 清晰的问题优先级（高/中/低）  
✅ 每个问题关联到具体提交者  
✅ 可统计的代码质量趋势  
✅ 节省大量人工审查时间  

**示例输出**:

| PR ID | PR 标题 | 提交者 | 问题级别 | 问题类别 | 问题描述 | 总体评分 |
|-------|---------|--------|----------|----------|----------|----------|
| 123 | 添加用户认证 | 张三 | 高 | 安全性 | 密码未加密存储 | 65 |
| 123 | 添加用户认证 | 张三 | 中 | 注释程度 | 关键函数缺少注释 | 65 |
| 124 | 修复登录bug | 李四 | 低 | 代码缺陷 | 边界条件检查不充分 | 85 |

---

## 🛠️ 技术支持

- 📖 **完整文档**: [WORKFLOW_CONFIG_README.md](./WORKFLOW_CONFIG_README.md)
- 🆚 **版本对比**: [VERSION_COMPARISON.md](./VERSION_COMPARISON.md)
- 📦 **文件清单**: [FILES_SUMMARY.md](./FILES_SUMMARY.md)
- 🔐 **凭据配置**: [credentials_setup_guide.json](./credentials_setup_guide.json)

---

## 📝 版本信息

- **N8N 版本**: 1.115.3
- **创建日期**: 2025-10-18
- **支持版本**: N8N 开源版、企业版、Cloud 版

---

## 🎉 开始使用

1. **确定版本**: 查看 [VERSION_COMPARISON.md](./VERSION_COMPARISON.md)
2. **快速部署**: 
   - 开源版 → [QUICKSTART_OPENSOURCE.md](./QUICKSTART_OPENSOURCE.md) ⭐
   - 企业版 → [QUICKSTART.md](./QUICKSTART.md)
3. **测试运行**: 手动执行 workflow 验证
4. **激活定时**: 开启自动运行

---

**祝你使用愉快！** 🎉  
如有问题或建议，欢迎反馈。
