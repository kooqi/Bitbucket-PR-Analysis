# 🚀 从这里开始

> 欢迎使用 N8N Bitbucket PR 自动分析工作流！

## 📋 第一步：选择你的版本

### 你使用哪种 Bitbucket？

| Bitbucket 类型 | 使用文件 | 阅读文档 |
|---------------|---------|---------|
| **自建 Bitbucket Server** 🏢 | `bitbucket_pr_analysis_workflow_selfhosted.json` ⭐ | [QUICKSTART_SELFHOSTED_BITBUCKET.md](./QUICKSTART_SELFHOSTED_BITBUCKET.md) |
| **Bitbucket Cloud**（官方云） | `bitbucket_pr_analysis_workflow_opensource.json` | [QUICKSTART_OPENSOURCE.md](./QUICKSTART_OPENSOURCE.md) |
| **Bitbucket Cloud**（企业版 N8N） | `bitbucket_pr_analysis_workflow.json` | [QUICKSTART.md](./QUICKSTART.md) |

---

## 🏢 自建 Bitbucket Server 用户（推荐使用这个）⭐

### 识别方法

你的 Bitbucket 访问地址是：
- ✅ `https://bitbucket.yourcompany.com`
- ✅ `https://git.yourcompany.com`
- ✅ 公司内部部署的 Bitbucket

### 配置要点

需要配置 **6 个**参数：

```javascript
BITBUCKET_API_URL         → https://bitbucket.company.com
BITBUCKET_PROJECT         → YOUR_PROJECT_KEY (大写)
BITBUCKET_REPO            → your-repo-slug
BITBUCKET_API_VERSION     → 1.0
FEISHU_APP_TOKEN          → bascnxxxxx
FEISHU_TABLE_ID           → tblxxxxx
```

### 快速开始

```bash
1️⃣ 导入: bitbucket_pr_analysis_workflow_selfhosted.json
2️⃣ 修改: "⚙️ 配置中心 (自建版配置)" 节点 (6个配置项)
3️⃣ 配置: 3个凭据
4️⃣ 创建飞书表格 (12个字段)
5️⃣ 测试并激活 ✅
```

📖 **详细步骤**: [QUICKSTART_SELFHOSTED_BITBUCKET.md](./QUICKSTART_SELFHOSTED_BITBUCKET.md)

---

## ☁️ Bitbucket Cloud 用户

### 识别方法

你的 Bitbucket 访问地址是：
- ✅ `https://bitbucket.org/your-workspace/your-repo`

### N8N 开源版（推荐）

需要配置 **4 个**参数：

```javascript
BITBUCKET_WORKSPACE       → your_workspace
BITBUCKET_REPO            → your_repo
FEISHU_APP_TOKEN          → bascnxxxxx
FEISHU_TABLE_ID           → tblxxxxx
```

**快速开始:**
```bash
1️⃣ 导入: bitbucket_pr_analysis_workflow_opensource.json
2️⃣ 修改: "⚙️ 配置中心" 节点 (4个配置项)
3️⃣ 配置: 3个凭据
4️⃣ 测试并激活
```

📖 **详细步骤**: [QUICKSTART_OPENSOURCE.md](./QUICKSTART_OPENSOURCE.md)

### N8N 企业版/Cloud 版

使用 Settings → Variables 配置：

```bash
1️⃣ Settings → Variables 添加 4 个环境变量
2️⃣ 导入: bitbucket_pr_analysis_workflow.json
3️⃣ 配置: 3个凭据
4️⃣ 测试并激活
```

📖 **详细步骤**: [QUICKSTART.md](./QUICKSTART.md)

---

## 🤔 不确定使用哪个版本？

### 快速判断流程图

```
你的 Bitbucket 地址是？
    │
    ├─ https://bitbucket.yourcompany.com
    │  └─→ 使用 selfhosted 版本 ⭐
    │
    └─ https://bitbucket.org/...
       └─→ Bitbucket Cloud
           │
           ├─ N8N 有 Variables 功能？
           │  ├─ 是 → 企业版 workflow
           │  └─ 否 → 开源版 workflow
           │
           └─ 不确定 → 开源版 workflow (通用)
```

---

## 📦 文件对照表

| 文件名 | Bitbucket 类型 | N8N 版本 | 配置数量 |
|-------|---------------|----------|---------|
| `bitbucket_pr_analysis_workflow_selfhosted.json` ⭐ | 自建 Server | 所有版本 | 6 个 |
| `bitbucket_pr_analysis_workflow_opensource.json` | Cloud | 开源版 | 4 个 |
| `bitbucket_pr_analysis_workflow.json` | Cloud | 企业版 | 4 个 |

---

## ✨ 核心功能（所有版本相同）

- ⏰ **每天 22:00** 自动运行
- 📥 **自动获取** 当天创建的 PRs
- 🤖 **千问 AI** 多维度评估
  - 代码缺陷检测
  - 注释程度评估
  - 合理性分析
  - 安全性审查
- 📊 **高/中/低** 三级问题分类
- 👥 **关联提交者**
- 📤 **飞书表格** List 输出

---

## 🎯 推荐选择

### 🏢 公司内部使用自建 Bitbucket → 选这个！

**文件**: `bitbucket_pr_analysis_workflow_selfhosted.json`  
**文档**: [QUICKSTART_SELFHOSTED_BITBUCKET.md](./QUICKSTART_SELFHOSTED_BITBUCKET.md)

**特点**:
- ✅ 支持自建 Bitbucket Server/Data Center
- ✅ 灵活配置 API 地址
- ✅ 适配 Project Key + Repo slug 结构
- ✅ 支持多种 API 版本

### ☁️ 使用 Bitbucket Cloud

**开源版 N8N**:  
- 文件: `bitbucket_pr_analysis_workflow_opensource.json`
- 文档: [QUICKSTART_OPENSOURCE.md](./QUICKSTART_OPENSOURCE.md)

**企业版 N8N**:  
- 文件: `bitbucket_pr_analysis_workflow.json`
- 文档: [QUICKSTART.md](./QUICKSTART.md)

---

## ⚡ 5 分钟部署（自建版）

```bash
# 1. 导入 workflow
bitbucket_pr_analysis_workflow_selfhosted.json

# 2. 修改配置节点
⚙️ 配置中心 (自建版配置)
  ├─ BITBUCKET_API_URL: https://bitbucket.company.com
  ├─ BITBUCKET_PROJECT: YOUR_PROJECT
  ├─ BITBUCKET_REPO: your-repo
  ├─ BITBUCKET_API_VERSION: 1.0
  ├─ FEISHU_APP_TOKEN: bascnxxxxx
  └─ FEISHU_TABLE_ID: tblxxxxx

# 3. 配置凭据
Bitbucket Credentials (Basic Auth)
Qianwen API Key (Header Auth)
Feishu Access Token (Header Auth)

# 4. 创建飞书表格（12 个字段）

# 5. 测试并激活 ✅
```

---

## 📚 相关文档

### 必读文档
- **自建 Bitbucket**: [QUICKSTART_SELFHOSTED_BITBUCKET.md](./QUICKSTART_SELFHOSTED_BITBUCKET.md) ⭐
- **开源版 N8N**: [QUICKSTART_OPENSOURCE.md](./QUICKSTART_OPENSOURCE.md)
- **企业版 N8N**: [QUICKSTART.md](./QUICKSTART.md)

### 进阶文档
- 版本对比: [VERSION_COMPARISON.md](./VERSION_COMPARISON.md)
- 详细配置: [WORKFLOW_CONFIG_README.md](./WORKFLOW_CONFIG_README.md)
- 凭据配置: [credentials_setup_guide.json](./credentials_setup_guide.json)

---

## ✅ 配置清单（自建版）

在开始之前，准备以下信息：

### Bitbucket Server 配置
- [ ] Bitbucket Server 访问地址
- [ ] Project Key（大写）
- [ ] Repository slug
- [ ] Personal Access Token 或密码

### 千问 API
- [ ] API Key（从阿里云 DashScope 获取）

### 飞书
- [ ] 多维表格 App Token
- [ ] 多维表格 Table ID
- [ ] 应用 Access Token

---

## ❓ 常见问题

### Q: 我如何知道是自建还是云版？

**A**: 看你的 Bitbucket 访问地址：
- `bitbucket.yourcompany.com` → 自建版
- `bitbucket.org` → 云版

### Q: 自建版和云版的 workflow 有什么区别？

**A**: 主要是 API 端点不同：
- **云版**: `api.bitbucket.org/2.0/repositories/{workspace}/{repo}`
- **自建版**: `your-server.com/rest/api/1.0/projects/{project}/repos/{repo}`

### Q: 我能同时监控多个仓库吗？

**A**: 需要为每个仓库创建一个 workflow，或者修改代码支持多仓库。

### Q: 配置节点在哪里？

**A**: 
- **自建版**: "⚙️ 配置中心 (自建版配置)"
- **开源版**: "⚙️ 配置中心 (请修改这里)"
- **企业版**: Settings → Variables

---

## 🎉 开始部署

根据你的情况选择：

1. **自建 Bitbucket** → [自建版快速开始](./QUICKSTART_SELFHOSTED_BITBUCKET.md) ⭐
2. **Bitbucket Cloud + 开源 N8N** → [开源版快速开始](./QUICKSTART_OPENSOURCE.md)
3. **Bitbucket Cloud + 企业 N8N** → [企业版快速开始](./QUICKSTART.md)
4. **不确定** → [版本对比](./VERSION_COMPARISON.md)

---

**祝你部署顺利！** 🚀

有问题随时查看对应的文档。
