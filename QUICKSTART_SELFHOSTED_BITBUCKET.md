# 自建 Bitbucket Server 快速开始指南

> 🏢 适用于自建的 Bitbucket Server / Bitbucket Data Center

## 🎯 自建版 vs 云版的区别

| 特性 | Bitbucket Cloud | Bitbucket Server (自建) |
|------|----------------|------------------------|
| API 端点 | `api.bitbucket.org` | `your-server.com` |
| 项目标识 | Workspace | Project Key |
| API 路径 | `/2.0/repositories/{workspace}/{repo}` | `/rest/api/1.0/projects/{PROJECT}/repos/{repo}` |
| 适用文件 | `*_opensource.json` | `*_selfhosted.json` ⭐ |

## 📦 使用文件

**`bitbucket_pr_analysis_workflow_selfhosted.json`** ⭐

---

## 🚀 5 分钟快速部署

### 步骤 1: 导入 Workflow

1. 打开你的 N8N 开源实例
2. 点击 **"+"** → **"Import from File"**
3. 选择 `bitbucket_pr_analysis_workflow_selfhosted.json`
4. 点击导入

### 步骤 2: 修改配置节点（⚙️ 最重要）

导入后，双击 **"⚙️ 配置中心 (自建版配置)"** 节点，修改 6 个配置项：

#### 配置项说明

| 配置项 | 说明 | 示例 |
|-------|------|------|
| **BITBUCKET_API_URL** | Bitbucket Server 地址 | `https://bitbucket.company.com` |
| **BITBUCKET_PROJECT** | 项目 Key（大写） | `PROJ` 或 `MYTEAM` |
| **BITBUCKET_REPO** | 仓库 slug（小写） | `my-repository` |
| **BITBUCKET_API_VERSION** | API 版本 | `1.0` 或 `latest` |
| **FEISHU_APP_TOKEN** | 飞书 App Token | `bascnXXXXXXXXXXXX` |
| **FEISHU_TABLE_ID** | 飞书表格 ID | `tblXXXXXXXXXXXX` |

#### 如何获取配置信息

##### 1. BITBUCKET_API_URL

你的 Bitbucket Server 访问地址（不包含路径）：

```
❌ 错误: https://bitbucket.company.com/projects/PROJ
✅ 正确: https://bitbucket.company.com
```

##### 2. BITBUCKET_PROJECT

从 Bitbucket Server URL 中获取项目 Key：

```
URL: https://bitbucket.company.com/projects/MYTEAM/repos/my-repo
                                             ^^^^^^
                                          Project Key (大写)
```

或者在项目设置中查看 "Project key"。

##### 3. BITBUCKET_REPO

从 URL 中获取仓库 slug（通常是小写的仓库名）：

```
URL: https://bitbucket.company.com/projects/MYTEAM/repos/my-repo
                                                           ^^^^^^^
                                                        Repository slug
```

##### 4. BITBUCKET_API_VERSION

Bitbucket Server API 版本：

- **`1.0`** - 适用于大多数版本（推荐）
- **`latest`** - 使用最新 API 版本
- **`2.0`** - 如果你的服务器支持

**建议**: 使用 `1.0`，最稳定

##### 5. FEISHU_APP_TOKEN 和 FEISHU_TABLE_ID

从飞书多维表格 URL 获取：

```
https://xxx.feishu.cn/base/bascnABCDEF123456?table=tblXYZ789
                           ↑                        ↑
                    FEISHU_APP_TOKEN         FEISHU_TABLE_ID
```

---

### 步骤 3: 配置凭据

需要配置 3 个凭据：

#### 3.1 Bitbucket Credentials (Basic Auth)

1. Credentials → Add Credential → **Basic Auth**
2. 名称：`Bitbucket Credentials`
3. 填入：
   - **Username**: 你的 Bitbucket 用户名
   - **Password**: 
     - **方式 1**: 你的 Bitbucket 密码
     - **方式 2**: Personal Access Token（推荐）

**创建 Personal Access Token:**

1. 登录 Bitbucket Server
2. 点击头像 → **Manage account**
3. 左侧菜单 → **Personal access tokens**
4. 点击 **Create a token**
5. 名称：`N8N Workflow`
6. 权限勾选：
   - ✅ **Project Read**
   - ✅ **Repository Read**
   - ✅ **Pull Request Read**
7. 点击 **Create**，复制 Token（只显示一次！）

#### 3.2 Qianwen API Key (Header Auth)

1. Credentials → Add Credential → **Header Auth**
2. 名称：`Qianwen API Key`
3. 填入：
   - **Header Name**: `Authorization`
   - **Header Value**: `Bearer sk-your-qianwen-api-key`

**获取千问 API Key:**
1. 访问: https://dashscope.console.aliyun.com/
2. 登录后进入 **API-KEY 管理**
3. 点击 **创建新的 API-KEY**
4. 复制 API Key
5. 在 N8N 中填入时加上前缀：`Bearer sk-xxxxxxxxx`

#### 3.3 Feishu Access Token (Header Auth)

1. Credentials → Add Credential → **Header Auth**
2. 名称：`Feishu Access Token`
3. 填入：
   - **Header Name**: `Authorization`
   - **Header Value**: `Bearer t-your-access-token`

**获取飞书 Access Token:**

使用应用凭证获取（2小时有效）：
```bash
curl -X POST 'https://open.feishu.cn/open-apis/auth/v3/tenant_access_token/internal' \
-H 'Content-Type: application/json' \
-d '{
  "app_id": "cli_xxxxxxxxxxxxx",
  "app_secret": "your_app_secret"
}'
```

响应中的 `tenant_access_token` 就是你需要的，格式：`Bearer t-xxxxxxxxx`

---

### 步骤 4: 创建飞书多维表格

1. 在飞书中创建新的多维表格
2. 添加以下 12 个字段（**字段名必须完全一致**）：

| 字段名 | 字段类型 | 说明 |
|-------|---------|------|
| **PR ID** | 文本 | Pull Request ID |
| **PR 标题** | 文本 | PR 标题 |
| **提交者** | 文本 | PR 作者 |
| **PR 链接** | URL | PR 链接 |
| **评估日期** | 日期时间 | 评估时间 |
| **问题级别** | 单选 | 选项：高/中/低/无 |
| **问题类别** | 单选 | 选项：代码缺陷/注释程度/合理性/安全性/无问题 |
| **问题描述** | 多行文本 | 问题详细描述 |
| **代码位置** | 文本 | 代码位置 |
| **改进建议** | 多行文本 | 改进建议 |
| **总体评分** | 文本 | 0-100 分 |
| **总结** | 多行文本 | 总体评价 |

---

### 步骤 5: 测试运行

1. 在 N8N 中打开导入的 workflow
2. 点击右上角的 **"Execute Workflow"** 按钮
3. 观察每个节点的执行：
   - ✅ 绿色 = 成功
   - ❌ 红色 = 失败（点击查看错误）
4. 检查飞书表格中是否有数据

### 步骤 6: 激活定时任务

1. 测试成功后，点击右上角的 **"Active"** 开关
2. Workflow 将在每天 22:00 自动运行

---

## 🔍 配置示例

### 完整配置示例

假设你的 Bitbucket Server 信息如下：

- 访问地址: `https://bitbucket.mycompany.com`
- 项目: Frontend Team (Key: `FE`)
- 仓库: web-app

**配置节点应该填入：**

```javascript
BITBUCKET_API_URL:      https://bitbucket.mycompany.com
BITBUCKET_PROJECT:      FE
BITBUCKET_REPO:         web-app
BITBUCKET_API_VERSION:  1.0
FEISHU_APP_TOKEN:       bascnABCDEF123456
FEISHU_TABLE_ID:        tblXYZ789
```

**对应的 API 调用将是：**

```
GET https://bitbucket.mycompany.com/rest/api/1.0/projects/FE/repos/web-app/pull-requests
```

---

## ⚠️ 常见问题排查

### 问题 1: Bitbucket API 返回 401/403 错误

**可能原因：**
- 凭据配置错误
- Personal Access Token 权限不足
- Token 已过期

**排查步骤：**

1. 验证凭据是否正确：
```bash
curl -u username:token \
https://your-bitbucket-server/rest/api/1.0/projects/YOUR_PROJECT/repos/your-repo/pull-requests
```

2. 检查 Token 权限：
   - 必须包含: Project Read, Repository Read, Pull Request Read

3. 确认 Token 未过期

### 问题 2: Bitbucket API 返回 404 错误

**可能原因：**
- Project Key 或 Repo slug 错误
- API URL 不正确
- API 版本不支持

**排查步骤：**

1. 确认 Project Key 是大写：
   ```
   ❌ 错误: project_key="myteam"
   ✅ 正确: project_key="MYTEAM"
   ```

2. 确认 Repo slug（通常是小写）：
   ```
   ❌ 错误: repo="My-Repository"
   ✅ 正确: repo="my-repository"
   ```

3. 在浏览器中访问 PR 页面，从 URL 复制正确的值：
   ```
   https://bitbucket.company.com/projects/PROJ/repos/my-repo/pull-requests/123
                                          ^^^^        ^^^^^^^
                                       Project      Repo slug
   ```

4. 测试 API 端点：
```bash
curl -u username:token \
https://your-server/rest/api/1.0/projects/YOUR_PROJECT/repos/your-repo/pull-requests
```

### 问题 3: 没有获取到今天的 PR

**可能原因：**
- 今天确实没有新创建的 PR
- 时区问题
- PR 状态不是 OPEN

**解决方法：**

1. 确认今天是否有新 PR
2. 检查 Parse PRs 节点的输出
3. 如果需要获取所有 PR（不限今天），修改 "Parse PRs" 节点：

```javascript
// 注释掉或删除日期过滤
// const todayPRs = prs.filter(pr => {
//   const createdDate = new Date(pr.createdDate);
//   return createdDate.getTime() >= todayTimestamp;
// });

// 直接使用所有 PR
const todayPRs = prs;
```

### 问题 4: Diff 内容为空

**可能原因：**
- Diff API 端点不正确
- PR 没有代码变更
- 权限问题

**解决方法：**

1. 检查 "Parse PRs" 节点输出的 `diff_url`
2. 手动测试 diff API：
```bash
curl -u username:token \
https://your-server/rest/api/1.0/projects/PROJECT/repos/repo/pull-requests/123/diff
```

3. 如果你的 Bitbucket Server 版本较老，可能需要使用不同的 API：
```
/rest/api/1.0/projects/{project}/repos/{repo}/pull-requests/{id}/changes
```

### 问题 5: SSL 证书错误

**可能原因：**
- 自签名证书
- 证书过期

**解决方法：**

在 "Bitbucket Get PRs" 和 "Get PR Diff" 节点中添加选项：

1. 打开节点设置
2. 展开 **Options**
3. 添加 **SSL Certificates**
4. 选择 **Ignore SSL Issues** (⚠️ 仅用于测试环境)

或者在 N8N 环境变量中设置：
```bash
NODE_TLS_REJECT_UNAUTHORIZED=0
```

---

## 🎯 工作流程

```
1. Schedule Trigger          → 每天 22:00 触发
2. ⚙️ 配置中心             → 存储配置（需修改）
3. Bitbucket Get PRs        → 调用自建 Bitbucket API
4. Parse PRs                → 解析并过滤今天的 PRs
5. Get PR Diff              → 获取代码变更
6. Prepare AI Prompt        → 构造评估提示词
7. Call Qianwen API         → AI 评估
8. Categorize Issues        → 问题分类（高/中/低）
9. Format for Feishu        → 格式化输出
10. Write to Feishu Table   → 写入飞书表格
```

---

## 📋 配置验证清单

在激活 workflow 之前，请确认：

### Bitbucket Server 配置
- [ ] BITBUCKET_API_URL 格式正确（无尾随斜杠）
- [ ] BITBUCKET_PROJECT 是大写的 Project Key
- [ ] BITBUCKET_REPO 是正确的 repository slug
- [ ] BITBUCKET_API_VERSION 填写正确（推荐 `1.0`）

### Bitbucket 凭据
- [ ] Username 正确
- [ ] Password/Token 有效
- [ ] Token 权限包含: Project Read, Repository Read, Pull Request Read

### 千问 API
- [ ] API Key 格式: `Bearer sk-xxxxx`
- [ ] 账户余额充足

### 飞书配置
- [ ] FEISHU_APP_TOKEN 正确
- [ ] FEISHU_TABLE_ID 正确
- [ ] Access Token 格式: `Bearer t-xxxxx`
- [ ] Token 未过期

### 飞书表格
- [ ] 12 个字段全部创建
- [ ] 字段名称完全一致（包括空格）
- [ ] 字段类型正确

### 测试验证
- [ ] 手动执行测试成功
- [ ] 所有节点显示绿色
- [ ] 飞书表格中有数据

---

## 💡 高级配置

### 修改 PR 过滤条件

如果你想获取特定分支的 PR，修改 "Bitbucket Get PRs" 节点的查询参数：

```javascript
// 添加额外的查询参数
{
  "name": "at",
  "value": "refs/heads/main"  // 目标分支
}
```

### 调整执行时间

修改 "Schedule Trigger" 节点的 cron 表达式：

```
0 22 * * *    # 每天 22:00
0 9 * * *     # 每天 9:00
0 */6 * * *   # 每 6 小时
0 9 * * 1-5   # 工作日 9:00
```

### 获取最近 N 天的 PR

修改 "Parse PRs" 节点中的日期过滤：

```javascript
// 获取最近 7 天的 PR
const sevenDaysAgo = new Date();
sevenDaysAgo.setDate(sevenDaysAgo.getDate() - 7);
const timestamp = sevenDaysAgo.getTime();

const recentPRs = prs.filter(pr => {
  const createdDate = new Date(pr.createdDate);
  return createdDate.getTime() >= timestamp;
});
```

---

## 📊 Bitbucket Server API 参考

### API 端点格式

```
# 获取 Pull Requests
GET /rest/api/{version}/projects/{projectKey}/repos/{repositorySlug}/pull-requests

# 获取 PR Diff
GET /rest/api/{version}/projects/{projectKey}/repos/{repositorySlug}/pull-requests/{pullRequestId}/diff

# 获取 PR Changes
GET /rest/api/{version}/projects/{projectKey}/repos/{repositorySlug}/pull-requests/{pullRequestId}/changes
```

### 常用查询参数

| 参数 | 说明 | 示例 |
|------|------|------|
| `state` | PR 状态 | `OPEN`, `MERGED`, `DECLINED`, `ALL` |
| `limit` | 返回数量限制 | `100` |
| `start` | 分页起始位置 | `0` |
| `at` | 特定分支 | `refs/heads/main` |
| `direction` | 方向 | `INCOMING`, `OUTGOING` |

### PR 对象结构

```json
{
  "id": 123,
  "title": "PR 标题",
  "description": "PR 描述",
  "state": "OPEN",
  "createdDate": 1234567890000,
  "updatedDate": 1234567890000,
  "author": {
    "user": {
      "name": "username",
      "displayName": "User Name",
      "emailAddress": "user@example.com"
    }
  },
  "fromRef": {
    "id": "refs/heads/feature-branch",
    "displayId": "feature-branch"
  },
  "toRef": {
    "id": "refs/heads/main",
    "displayId": "main"
  }
}
```

---

## 🔗 相关链接

- Bitbucket Server REST API 文档: https://docs.atlassian.com/bitbucket-server/rest/
- 千问 API 文档: https://help.aliyun.com/document_detail/2712195.html
- 飞书开放平台: https://open.feishu.cn/

---

## 📞 获取帮助

如果遇到问题：

1. 检查本文档的 **常见问题排查** 章节
2. 验证配置节点中的 6 个配置项
3. 测试 Bitbucket API 是否可访问
4. 查看 N8N 执行日志

---

**祝你部署顺利！** 🎉

如有问题，欢迎反馈。
