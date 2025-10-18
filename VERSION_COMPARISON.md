# N8N Workflow 版本对比

## 📦 文件列表

| 文件名 | 适用版本 | 说明 |
|-------|---------|------|
| `bitbucket_pr_analysis_workflow.json` | N8N 企业版 / Cloud 版 | 使用环境变量（Variables 功能） |
| `bitbucket_pr_analysis_workflow_opensource.json` | **N8N 开源版** ⭐ | 使用配置节点，无需 Variables |

## 🔍 主要区别

### 企业版 / Cloud 版
```json
{
  "url": "https://api.bitbucket.org/2.0/repositories/{{ $env.BITBUCKET_WORKSPACE }}/..."
}
```
- ✅ 使用 N8N Variables 功能
- ✅ 配置集中在 Settings → Variables
- ✅ 适合多个 workflow 共享配置
- ❌ 开源版不支持

### 开源版（推荐）
```json
{
  "nodes": [
    {
      "name": "⚙️ 配置中心 (请修改这里)",
      "type": "n8n-nodes-base.set",
      "parameters": {
        "assignments": {
          "assignments": [
            { "name": "BITBUCKET_WORKSPACE", "value": "your_workspace" }
          ]
        }
      }
    }
  ]
}
```
- ✅ 使用 Set 节点存储配置
- ✅ 所有配置在 workflow 内部
- ✅ 开源版完全支持
- ✅ 配置直观，易于修改

## 📊 详细对比

| 特性 | 企业版 Workflow | 开源版 Workflow |
|------|----------------|----------------|
| **配置方式** | Settings → Variables | 配置节点（Set 节点） |
| **配置位置** | 全局环境变量 | Workflow 内部 |
| **节点数量** | 9 个 | 10 个（多一个配置节点） |
| **配置难度** | 简单（集中配置） | 简单（双击节点修改） |
| **可移植性** | 需要重新设置 Variables | 导入即用（修改配置节点） |
| **多 Workflow 共享** | ✅ 支持 | ❌ 每个 workflow 独立配置 |
| **开源版支持** | ❌ 不支持 | ✅ 完全支持 |
| **配置可见性** | 需进入 Settings 查看 | 直接在 workflow 中可见 |
| **版本控制** | 配置和代码分离 | 配置和代码一体 |

## 🎯 如何选择

### 使用企业版 Workflow（如果你有）
- ✅ 你使用 N8N Cloud
- ✅ 你使用 N8N 企业版（自托管）
- ✅ 你有多个 workflow 需要共享配置
- ✅ 你希望配置和 workflow 分离管理

### 使用开源版 Workflow（推荐）
- ✅ 你使用 N8N 开源版（自托管）⭐
- ✅ 你不确定你的 N8N 版本
- ✅ 你希望配置更直观
- ✅ 你希望 workflow 完全独立
- ✅ 你希望更好的可移植性

## 🔄 如何判断你的 N8N 版本

### 方法 1: 检查 Variables 功能
1. 登录 N8N
2. 点击左侧菜单 **Settings**
3. 查看是否有 **Variables** 选项

- ✅ 有 Variables 选项 → 企业版 / Cloud 版（两个版本都可用）
- ❌ 没有 Variables 选项 → 开源版（只能用开源版 workflow）

### 方法 2: 检查版本信息
1. 点击右上角的 **?** （帮助）
2. 查看 **About n8n**
3. 查看版本号和许可证类型

- `Community Edition` → 开源版
- `Enterprise Edition` → 企业版
- `Cloud` → Cloud 版

### 方法 3: 尝试导入
1. 先尝试导入 `bitbucket_pr_analysis_workflow_opensource.json`（开源版）
2. 如果你的 N8N 是企业版，开源版也能正常工作
3. 如果你想使用 Variables，可以再导入企业版

**建议**: 如果不确定，直接使用开源版 workflow，它在所有版本中都能工作！

## 🔧 配置说明

### 企业版配置步骤

1. **设置环境变量** (Settings → Variables)
```
BITBUCKET_WORKSPACE = your_workspace_name
BITBUCKET_REPO = your_repository_name
FEISHU_APP_TOKEN = bascnxxxxx
FEISHU_TABLE_ID = tblxxxxx
```

2. **导入 workflow**
```
bitbucket_pr_analysis_workflow.json
```

3. **配置凭据**（同开源版）

### 开源版配置步骤

1. **导入 workflow**
```
bitbucket_pr_analysis_workflow_opensource.json
```

2. **修改配置节点**
- 双击 **"⚙️ 配置中心 (请修改这里)"** 节点
- 修改 4 个配置项
- 保存

3. **配置凭据**（同企业版）

## 📚 文档指引

### 企业版文档
- 快速开始: `QUICKSTART.md`
- 详细配置: `WORKFLOW_CONFIG_README.md`
- 环境变量示例: `.env.example`

### 开源版文档
- **快速开始**: `QUICKSTART_OPENSOURCE.md` ⭐ **从这里开始**
- 详细配置: `WORKFLOW_CONFIG_README.md`（部分内容通用）
- 凭据配置: `credentials_setup_guide.json`

## 🎯 功能对比

两个版本的功能完全相同：

- ✅ 每天 22:00 自动触发
- ✅ 从 Bitbucket 获取当天的 PRs
- ✅ 使用千问大模型评估代码质量
- ✅ 按高中低三个级别分类问题
- ✅ 关联提交者信息
- ✅ 输出到飞书多维表格

**唯一区别**: 配置方式不同

## 💡 最佳实践

### 开源版用户
1. 使用 `bitbucket_pr_analysis_workflow_opensource.json`
2. 在配置节点中设置所有参数
3. 如果有多个类似 workflow，可以复制配置节点的值

### 企业版用户
1. 如果只有一个 Bitbucket 项目，使用开源版 workflow 更直观
2. 如果有多个项目需要监控，使用企业版 workflow + Variables
3. 可以为每个项目创建一个 workflow，通过 Variables 切换配置

### 版本迁移

**从开源版迁移到企业版**:
1. 记录配置节点中的 4 个值
2. 在 Settings → Variables 中创建对应的变量
3. 导入企业版 workflow

**从企业版迁移到开源版**:
1. 记录 Variables 中的 4 个值
2. 导入开源版 workflow
3. 在配置节点中填入这些值

## 📝 配置模板

### 企业版 Variables 配置
```bash
# Settings → Variables
BITBUCKET_WORKSPACE: my-workspace
BITBUCKET_REPO: my-project
FEISHU_APP_TOKEN: bascnXXXXXXXXXXXXXXXXXXX
FEISHU_TABLE_ID: tblXXXXXXXXXXXXXXXXXXX
```

### 开源版配置节点设置
```javascript
// ⚙️ 配置中心 (请修改这里) 节点
{
  "BITBUCKET_WORKSPACE": "my-workspace",
  "BITBUCKET_REPO": "my-project",
  "FEISHU_APP_TOKEN": "bascnXXXXXXXXXXXXXXXXXXX",
  "FEISHU_TABLE_ID": "tblXXXXXXXXXXXXXXXXXXX"
}
```

## ❓ FAQ

### Q: 我不知道我的 N8N 是哪个版本？
**A**: 直接使用开源版 workflow (`bitbucket_pr_analysis_workflow_opensource.json`)，它在所有版本中都能正常工作！

### Q: 开源版 workflow 在企业版中能用吗？
**A**: 可以！开源版使用的是基础节点（Set 节点），所有 N8N 版本都支持。

### Q: 企业版 workflow 在开源版中能用吗？
**A**: 不能。因为开源版不支持 Variables 功能，导入后会报错。

### Q: 我应该用哪个版本？
**A**: 
- 如果你是开源版用户 → 必须使用开源版 workflow
- 如果你是企业版用户 → 两个都可以，推荐开源版（更直观）
- 如果你不确定 → 使用开源版 workflow

### Q: 两个版本性能有差异吗？
**A**: 没有。功能和性能完全相同，只是配置方式不同。

### Q: 配置节点会影响性能吗？
**A**: 不会。配置节点只在 workflow 开始时执行一次，对性能无影响。

### Q: 能同时导入两个版本吗？
**A**: 可以，但没必要。功能完全相同，选择适合你的版本即可。

---

## 🎉 总结

| 如果你是... | 推荐使用 | 文档 |
|------------|----------|------|
| **N8N 开源版用户** | `bitbucket_pr_analysis_workflow_opensource.json` | `QUICKSTART_OPENSOURCE.md` |
| **N8N 企业版用户（单项目）** | `bitbucket_pr_analysis_workflow_opensource.json` | `QUICKSTART_OPENSOURCE.md` |
| **N8N 企业版用户（多项目）** | `bitbucket_pr_analysis_workflow.json` | `QUICKSTART.md` |
| **N8N Cloud 用户** | `bitbucket_pr_analysis_workflow.json` | `QUICKSTART.md` |
| **不确定版本** | `bitbucket_pr_analysis_workflow_opensource.json` | `QUICKSTART_OPENSOURCE.md` |

**万能建议**: 使用开源版 workflow (`bitbucket_pr_analysis_workflow_opensource.json`)，适用于所有 N8N 版本！ 🎯
