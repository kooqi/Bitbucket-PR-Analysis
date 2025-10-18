# 🚀 从这里开始

> 欢迎使用 N8N Bitbucket PR 自动分析工作流！

## 📋 你只需要 3 步

### 第 1 步: 确定你的 N8N 版本

#### 方法 1: 检查 Variables 功能
1. 登录 N8N
2. 点击左侧 **Settings**
3. 查看是否有 **Variables** 选项

结果：
- ✅ **有** Variables → 可以使用任意版本（建议用开源版）
- ❌ **没有** Variables → 必须使用开源版

#### 方法 2: 直接使用（推荐）

**不确定？直接使用开源版！** 它在所有 N8N 版本中都能工作。

---

### 第 2 步: 选择对应的文件和文档

| 你的版本 | 使用文件 | 阅读文档 |
|---------|---------|---------|
| **开源版或不确定** ⭐ | `bitbucket_pr_analysis_workflow_opensource.json` | [QUICKSTART_OPENSOURCE.md](./QUICKSTART_OPENSOURCE.md) |
| 企业版/Cloud版 | `bitbucket_pr_analysis_workflow.json` | [QUICKSTART.md](./QUICKSTART.md) |

---

### 第 3 步: 按照快速开始文档操作

#### 开源版用户（5 分钟）

1. 导入 `bitbucket_pr_analysis_workflow_opensource.json`
2. 双击 **"⚙️ 配置中心"** 节点，修改 4 个配置项：
   ```
   BITBUCKET_WORKSPACE
   BITBUCKET_REPO
   FEISHU_APP_TOKEN
   FEISHU_TABLE_ID
   ```
3. 配置 3 个凭据：
   - Bitbucket Credentials (Basic Auth)
   - Qianwen API Key (Header Auth)
   - Feishu Access Token (Header Auth)
4. 创建飞书表格并添加 12 个字段
5. 测试运行 → 激活定时任务

📖 **详细步骤**: [QUICKSTART_OPENSOURCE.md](./QUICKSTART_OPENSOURCE.md)

#### 企业版用户（5 分钟）

1. Settings → Variables，添加 4 个环境变量
2. 导入 `bitbucket_pr_analysis_workflow.json`
3. 配置 3 个凭据（同开源版）
4. 创建飞书表格（同开源版）
5. 测试运行 → 激活定时任务

📖 **详细步骤**: [QUICKSTART.md](./QUICKSTART.md)

---

## 🎯 工作流功能

- ⏰ **每天 22:00 自动运行**
- 📥 **自动获取当天的 Bitbucket PRs**
- 🤖 **使用千问 AI 评估代码质量**
  - 代码缺陷
  - 注释程度
  - 合理性
  - 安全性
- 📊 **按高/中/低分类问题**
- 👥 **关联到提交者**
- 📤 **输出到飞书多维表格**

---

## 📦 文件导航

### 必读文档（按需选择）

```
开源版用户:
  START_HERE.md (你在这里)
    ↓
  QUICKSTART_OPENSOURCE.md ⭐ (必读)
    ↓
  credentials_setup_guide.json (配置凭据时参考)

企业版用户:
  START_HERE.md (你在这里)
    ↓
  QUICKSTART.md (必读)
    ↓
  credentials_setup_guide.json (配置凭据时参考)

不确定版本:
  START_HERE.md (你在这里)
    ↓
  VERSION_COMPARISON.md (版本对比)
    ↓
  QUICKSTART_OPENSOURCE.md (推荐使用开源版)
```

### 进阶文档（可选阅读）

- `WORKFLOW_CONFIG_README.md` - 详细配置说明和优化建议
- `FILES_SUMMARY.md` - 项目结构和文件清单
- `README.md` - 项目总览

---

## ✅ 配置清单

在开始之前，请准备以下信息：

### Bitbucket 配置
- [ ] Workspace 名称（从 URL 获取）
- [ ] Repository 名称（从 URL 获取）
- [ ] App Password（需创建，权限: `pullrequest:read`, `repository:read`）

### 千问 API 配置
- [ ] API Key（从阿里云 DashScope 获取）
- [ ] 账户余额充足（按 token 计费）

### 飞书配置
- [ ] 多维表格 App Token（从表格 URL 获取）
- [ ] 多维表格 Table ID（从表格 URL 获取）
- [ ] Access Token（通过 App ID 和 Secret 获取）

---

## 🎬 开始部署

### 极速部署（开源版）⭐

```bash
# 1. 导入 workflow
bitbucket_pr_analysis_workflow_opensource.json

# 2. 修改配置节点（双击节点）
⚙️ 配置中心 (请修改这里)
  ├─ BITBUCKET_WORKSPACE: your_workspace
  ├─ BITBUCKET_REPO: your_repo
  ├─ FEISHU_APP_TOKEN: bascnxxxxx
  └─ FEISHU_TABLE_ID: tblxxxxx

# 3. 配置凭据
Bitbucket Credentials (Basic Auth)
Qianwen API Key (Header Auth)
Feishu Access Token (Header Auth)

# 4. 创建飞书表格（12 个字段）

# 5. 测试并激活 ✅
```

详细步骤见: [QUICKSTART_OPENSOURCE.md](./QUICKSTART_OPENSOURCE.md)

---

## ❓ 遇到问题？

### 常见问题速查

| 问题 | 文档位置 |
|------|---------|
| 如何判断 N8N 版本？ | [VERSION_COMPARISON.md](./VERSION_COMPARISON.md#如何判断你的-n8n-版本) |
| 如何配置凭据？ | [credentials_setup_guide.json](./credentials_setup_guide.json) |
| Bitbucket 401 错误？ | [QUICKSTART_OPENSOURCE.md](./QUICKSTART_OPENSOURCE.md#问题-2-bitbucket-api-返回-401-错误) |
| 千问 API 失败？ | [QUICKSTART_OPENSOURCE.md](./QUICKSTART_OPENSOURCE.md#问题-3-千问-api-调用失败) |
| 飞书写入失败？ | [QUICKSTART_OPENSOURCE.md](./QUICKSTART_OPENSOURCE.md#问题-4-飞书写入失败) |
| 没有获取到 PR？ | [QUICKSTART_OPENSOURCE.md](./QUICKSTART_OPENSOURCE.md#问题-5-没有获取到-pr) |

### 获取帮助

1. 先查看对应的快速开始文档
2. 查看 [WORKFLOW_CONFIG_README.md](./WORKFLOW_CONFIG_README.md) 故障排查章节
3. 检查 N8N 执行日志
4. 验证凭据配置

---

## 🎉 完成后你将获得

- 📊 **每日自动报告**: 无需人工干预
- 🎯 **问题分级**: 高/中/低优先级
- 👥 **责任明确**: 关联到具体提交者
- 📈 **质量趋势**: 可统计分析
- ⏰ **时间节省**: 自动化审查

---

## 📞 技术支持

- 📧 文档问题: 查看相关文档
- 💻 配置问题: 参考 [QUICKSTART_OPENSOURCE.md](./QUICKSTART_OPENSOURCE.md)
- 🔧 高级配置: 阅读 [WORKFLOW_CONFIG_README.md](./WORKFLOW_CONFIG_README.md)

---

**准备好了吗？** 

👉 [开源版快速开始](./QUICKSTART_OPENSOURCE.md)  
👉 [企业版快速开始](./QUICKSTART.md)  
👉 [版本对比和选择](./VERSION_COMPARISON.md)

---

**祝你部署顺利！** 🚀
