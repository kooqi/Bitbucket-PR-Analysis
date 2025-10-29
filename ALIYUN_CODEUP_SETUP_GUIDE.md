# 阿里云云效 Codeup PR 分析工作流配置指南

## 📖 概述

本工作流使用阿里云云效 Codeup 的 **GitLab 兼容 API v4** 来获取 Merge Request 信息并进行 AI 代码审查。

---

## 🔧 API 端点说明

### 使用的 API 版本

- **API 基础 URL**: `https://codeup.aliyun.com/api/v4`
- **API 版本**: GitLab API v4（阿里云云效 Codeup 兼容）
- **认证方式**: Private Token (HTTP Header)

### 主要端点

| 端点 | 方法 | 说明 |
|------|------|------|
| `/api/v4/projects/:id/merge_requests` | GET | 获取项目的 MR 列表 |
| `/api/v4/projects/:id/merge_requests/:mr_iid/changes` | GET | 获取指定 MR 的代码变更 |

参考文档：https://help.aliyun.com/document_detail/153710.html

---

## 🚀 快速配置

### 步骤 1: 获取 Private Token

1. 登录阿里云云效：https://codeup.aliyun.com
2. 点击右上角头像 → **个人设置**
3. 左侧菜单选择 **访问令牌** (Access Tokens)
4. 点击 **生成新令牌** 按钮
5. 填写信息：
   - **名称**: 填写令牌用途，如 "N8N PR Analysis"
   - **过期时间**: 建议选择较长时间或不过期
   - **权限范围**: 勾选以下权限
     - ✅ `api` - 完整的 API 访问权限
     - ✅ `read_repository` - 读取仓库
     - ✅ `read_api` - 读取 API
6. 点击 **创建令牌**
7. **重要**: 复制并保存生成的令牌（只显示一次）

### 步骤 2: 获取项目 ID

有两种方法获取项目 ID：

#### 方法 1: 从项目页面获取
1. 进入你的代码仓库主页
2. 在页面左侧边栏，项目名称下方可以看到 **Project ID**
3. 例如：`Project ID: 12345`

#### 方法 2: 通过 API 获取
```bash
curl --header "PRIVATE-TOKEN: your_access_token" \
     "https://codeup.aliyun.com/api/v4/projects?search=your_repo_name"
```

返回结果中的 `id` 字段就是项目 ID。

---

## ⚙️ N8N 工作流配置

### 1. 导入工作流文件

在 N8N 中导入 `aliyun_codeup_pr_analysis_workflow_opensource.json`

### 2. 配置中心节点

双击 **"⚙️ 配置中心 (请修改这里)"** 节点，修改以下配置：

```javascript
ALIYUN_ORGANIZATION_ID  →  your_organization_id  // 企业 ID（可选，暂未使用）
ALIYUN_REPO_ID          →  12345                // 项目 ID（必填）
FEISHU_APP_TOKEN        →  bascnxxxxx          // 飞书 App Token
FEISHU_TABLE_ID         →  tblxxxxx            // 飞书表格 ID
```

**说明**：
- `ALIYUN_REPO_ID` 是最重要的配置，填写你的项目 ID
- `ALIYUN_ORGANIZATION_ID` 为预留字段，当前工作流中未使用

### 3. 配置凭据

#### 阿里云云效 Codeup Access Token

1. 在 N8N 中，进入 **Credentials → Add Credential**
2. 选择 **Header Auth**
3. 填写信息：
   - **Credential Name**: `Aliyun Codeup Access Token`
   - **Name**: `PRIVATE-TOKEN`
   - **Value**: `your_private_token_here`（步骤1获取的令牌）
4. 点击 **Create**

**注意**：Header 名称必须是 `PRIVATE-TOKEN`，这是 GitLab API 的标准认证方式。

#### 千问 API 凭据

参考原有配置，使用 Header Auth：
- **Name**: `Authorization`
- **Value**: `Bearer sk-your-qianwen-api-key`

#### 飞书凭据

参考原有配置，使用 Header Auth：
- **Name**: `Authorization`
- **Value**: `Bearer t-your-feishu-access-token`

---

## 📊 API 查询参数说明

### 获取 MR 列表的参数

```javascript
{
  "state": "opened",           // MR 状态：opened（打开）、closed（关闭）、merged（已合并）
  "order_by": "created_at",    // 排序字段：created_at、updated_at
  "sort": "desc",              // 排序方式：desc（降序）、asc（升序）
  "created_after": "2025-10-28" // 筛选创建时间（YYYY-MM-DD 格式）
}
```

工作流默认获取 **昨天创建的、状态为打开** 的 MR。

### 自定义筛选条件

如果需要修改筛选条件，可以在 **Aliyun Codeup Get MRs** 节点中调整查询参数：

```javascript
// 示例 1: 获取最近 7 天的 MR
"created_after": "={{ $now.minus({days: 7}).toFormat('yyyy-MM-dd') }}"

// 示例 2: 获取所有状态的 MR
删除 "state" 参数或改为 "all"

// 示例 3: 按更新时间排序
"order_by": "updated_at"
```

---

## 🔍 API 响应格式

### MR 列表响应示例

```json
[
  {
    "id": 123,
    "iid": 1,
    "title": "添加新功能",
    "description": "这是一个新功能的描述",
    "state": "opened",
    "created_at": "2025-10-28T10:00:00Z",
    "updated_at": "2025-10-28T15:30:00Z",
    "source_branch": "feature/new-feature",
    "target_branch": "master",
    "author": {
      "id": 456,
      "name": "张三",
      "username": "zhangsan",
      "email": "zhangsan@example.com"
    },
    "web_url": "https://codeup.aliyun.com/org/repo/-/merge_requests/1",
    "project_id": 12345
  }
]
```

### MR Changes 响应示例

```json
{
  "id": 123,
  "iid": 1,
  "changes": [
    {
      "old_path": "src/main.js",
      "new_path": "src/main.js",
      "diff": "@@ -1,3 +1,4 @@\n function test() {\n+  console.log('new line');\n   return true;\n }"
    }
  ]
}
```

---

## 🎯 工作流节点说明

### 节点流程

```
Schedule Trigger (定时触发)
    ↓
⚙️ 配置中心 (配置管理)
    ↓
Aliyun Codeup Get MRs (获取 MR 列表)
    ↓
Parse MRs (解析 MR 数据)
    ↓
Get MR Diff (获取代码变更) - 循环处理每个 MR
    ↓
Prepare AI Prompt (准备 AI 提示词)
    ↓
Call Qianwen API (调用千问 API)
    ↓
Categorize Issues (问题分类)
    ↓
Format for Feishu (格式化输出)
    ↓
Write to Feishu Table (写入飞书)
```

---

## 🐛 常见问题排查

### 问题 1: 401 Unauthorized

**原因**：
- Private Token 无效或已过期
- Header 名称错误

**解决方法**：
1. 检查凭据配置，确认 Header 名称为 `PRIVATE-TOKEN`
2. 重新生成 Access Token
3. 确认令牌权限包含 `api` 和 `read_repository`

### 问题 2: 404 Not Found

**原因**：
- 项目 ID 错误
- API 端点 URL 错误

**解决方法**：
1. 确认项目 ID 是数字类型，不是项目名称
2. 检查 API URL 是否为 `https://codeup.aliyun.com/api/v4/...`
3. 确认有权限访问该项目

### 问题 3: 没有返回 MR 数据

**原因**：
- 筛选条件过于严格
- 时间范围内没有新的 MR

**解决方法**：
1. 检查 `created_after` 时间范围
2. 临时移除 `state` 参数，查看所有状态的 MR
3. 手动调用 API 验证：
```bash
curl --header "PRIVATE-TOKEN: your_token" \
     "https://codeup.aliyun.com/api/v4/projects/12345/merge_requests?state=opened"
```

### 问题 4: 无法获取 Diff 数据

**原因**：
- MR 还没有代码变更
- MR ID 解析错误

**解决方法**：
1. 在 Parse MRs 节点中检查 `mr_id` 是否正确提取
2. 确认使用的是 `iid`（Merge Request 内部 ID），不是 `id`
3. 查看 API 响应，确认 changes 数据结构

---

## 🔒 安全注意事项

1. **Token 安全**：
   - 不要将 Private Token 提交到代码仓库
   - 定期轮换 Token
   - 为不同用途创建不同的 Token

2. **权限最小化**：
   - 只授予必要的 API 权限
   - 使用只读权限（read_api、read_repository）

3. **数据安全**：
   - MR 代码会发送到千问 API 进行分析
   - 确保符合公司的数据安全政策
   - 对于敏感项目，考虑使用私有部署的 AI 模型

---

## 📚 参考资源

- 阿里云云效文档：https://help.aliyun.com/product/153741.html
- Codeup API 文档：https://help.aliyun.com/document_detail/153710.html
- GitLab API v4 文档：https://docs.gitlab.com/ee/api/
- 千问 API 文档：https://help.aliyun.com/document_detail/2712195.html

---

## 💡 高级配置

### 自定义筛选特定分支的 MR

在 **Aliyun Codeup Get MRs** 节点中添加参数：

```javascript
{
  "name": "source_branch",
  "value": "feature/*"
}
```

### 获取特定作者的 MR

添加参数：

```javascript
{
  "name": "author_username",
  "value": "zhangsan"
}
```

### 修改 diff 内容长度限制

在 **Prepare AI Prompt** 节点中修改：

```javascript
const maxDiffLength = 12000; // 默认 8000，可根据需要调整
```

---

## 🎉 测试验证

### 手动测试 API

使用 curl 命令测试 API 是否正常：

```bash
# 测试 1: 获取 MR 列表
curl --header "PRIVATE-TOKEN: your_token" \
     "https://codeup.aliyun.com/api/v4/projects/12345/merge_requests?state=opened"

# 测试 2: 获取特定 MR 的变更
curl --header "PRIVATE-TOKEN: your_token" \
     "https://codeup.aliyun.com/api/v4/projects/12345/merge_requests/1/changes"
```

### 在 N8N 中测试工作流

1. 手动执行工作流（点击 "Execute Workflow"）
2. 检查每个节点的输入输出
3. 确认数据正确写入飞书表格
4. 查看执行日志排查问题

---

**配置完成！** 🎉  
如有问题，请参考常见问题排查部分或查阅官方文档。
