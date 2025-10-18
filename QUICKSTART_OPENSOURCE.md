# N8N 开源版快速开始指南

> 🎉 本版本专为 N8N 开源版本设计，无需 Variables 功能！

## 📌 文件说明

使用 **`bitbucket_pr_analysis_workflow_opensource.json`** 文件进行导入。

## 🚀 5 分钟快速部署

### 步骤 1: 导入 Workflow

1. 打开你的 N8N 开源实例
2. 点击右上角的 **"+"** → **"Import from File"**
3. 选择 `bitbucket_pr_analysis_workflow_opensource.json`
4. 点击导入

### 步骤 2: 修改配置节点（⚙️ 最重要的一步）

导入后，你会看到第二个节点名为 **"⚙️ 配置中心 (请修改这里)"**

1. **双击**打开这个节点
2. 修改以下 4 个配置项：

```
BITBUCKET_WORKSPACE    →  你的 Bitbucket 工作空间名称
BITBUCKET_REPO         →  你的仓库名称
FEISHU_APP_TOKEN       →  飞书多维表格 App Token (格式: bascnxxxxx)
FEISHU_TABLE_ID        →  飞书表格 ID (格式: tblxxxxx)
```

**配置示例：**

| 配置项 | 示例值 | 说明 |
|--------|--------|------|
| BITBUCKET_WORKSPACE | `my-company` | 从 URL 获取: `bitbucket.org/{workspace}/...` |
| BITBUCKET_REPO | `my-project` | 从 URL 获取: `bitbucket.org/.../{{repo}}` |
| FEISHU_APP_TOKEN | `bascnXXXXXXXXXXXXXXXXXXX` | 从飞书表格 URL 获取 |
| FEISHU_TABLE_ID | `tblXXXXXXXXXXXXXXXXXXX` | 从飞书表格 URL 获取 |

**获取飞书配置的方法：**

飞书多维表格 URL 格式：
```
https://xxx.feishu.cn/base/{FEISHU_APP_TOKEN}?table={FEISHU_TABLE_ID}
```

例如：
```
https://xxx.feishu.cn/base/bascnABCDEF123456?table=tblXYZ789
                           ↑                        ↑
                    FEISHU_APP_TOKEN         FEISHU_TABLE_ID
```

3. 点击 **"Save"** 保存配置节点

### 步骤 3: 配置凭据

你需要配置 3 个凭据：

#### 3.1 Bitbucket 凭据 (Basic Auth)

1. 点击左侧菜单 **"Credentials"** → **"Add Credential"**
2. 选择 **"Basic Auth"**
3. 设置名称：`Bitbucket Credentials`
4. 填入：
   - **Username**: 你的 Bitbucket 用户名
   - **Password**: Bitbucket App Password

**如何创建 Bitbucket App Password:**
1. 登录 Bitbucket → 点击头像 → **Personal settings**
2. 左侧菜单 → **App passwords**
3. 点击 **Create app password**
4. 名称：`N8N Workflow`
5. 权限勾选：
   - ✅ **Repositories: Read**
   - ✅ **Pull requests: Read**
6. 点击 **Create**，复制生成的密码（只显示一次！）

#### 3.2 千问 API 凭据 (Header Auth)

1. Credentials → Add Credential → **Header Auth**
2. 设置名称：`Qianwen API Key`
3. 填入：
   - **Header Name**: `Authorization`
   - **Header Value**: `Bearer sk-your-qianwen-api-key`

**如何获取千问 API Key:**
1. 访问阿里云 DashScope: https://dashscope.console.aliyun.com/
2. 登录后进入 **API-KEY 管理**
3. 点击 **创建新的 API-KEY**
4. 复制 API Key（格式: `sk-xxxxxxxxx`）
5. 在 N8N 中填入时加上前缀：`Bearer sk-xxxxxxxxx`

#### 3.3 飞书凭据 (Header Auth)

1. Credentials → Add Credential → **Header Auth**
2. 设置名称：`Feishu Access Token`
3. 填入：
   - **Header Name**: `Authorization`
   - **Header Value**: `Bearer t-your-access-token`

**如何获取飞书 Access Token:**

方式 1: 使用应用凭证（推荐，2小时有效）
```bash
curl -X POST 'https://open.feishu.cn/open-apis/auth/v3/tenant_access_token/internal' \
-H 'Content-Type: application/json' \
-d '{
  "app_id": "cli_xxxxxxxxxxxxx",
  "app_secret": "your_app_secret"
}'
```

方式 2: 飞书开放平台
1. 访问 https://open.feishu.cn/
2. 创建企业自建应用
3. 应用详情 → **凭证与基础信息** → 获取 App ID 和 App Secret
4. 添加应用权限：
   - ✅ `bitable:app` (多维表格应用访问)
   - ✅ `bitable:record:write` (记录写入权限)
5. 使用上面的 curl 命令获取 tenant_access_token
6. 在 N8N 中填入：`Bearer t-xxxxxxxxx`

**注意**: tenant_access_token 有效期为 2 小时，建议使用 N8N 的 Feishu 节点自动刷新（可选优化）

### 步骤 4: 创建飞书多维表格

1. 在飞书中创建一个新的多维表格
2. 添加以下字段（顺序可以不同，但名称必须完全一致）：

| 字段名 | 字段类型 | 说明 |
|-------|---------|------|
| **PR ID** | 文本 | Pull Request ID |
| **PR 标题** | 文本 | PR 标题 |
| **提交者** | 文本 | PR 作者 |
| **PR 链接** | URL | PR 的链接 |
| **评估日期** | 日期时间 | 评估时间 |
| **问题级别** | 单选 | 选项：高/中/低/无 |
| **问题类别** | 单选 | 选项：代码缺陷/注释程度/合理性/安全性/无问题 |
| **问题描述** | 多行文本 | 问题详细描述 |
| **代码位置** | 文本 | 问题所在位置 |
| **改进建议** | 多行文本 | 改进建议 |
| **总体评分** | 文本 | 0-100 分 |
| **总结** | 多行文本 | 总体评价 |

**重要**: 字段名称必须与上表完全一致，包括空格和中文字符！

### 步骤 5: 测试运行

1. 在 N8N 中打开导入的 workflow
2. 点击右上角的 **"Execute Workflow"** 按钮
3. 观察每个节点的执行情况：
   - ✅ 绿色 = 成功
   - ❌ 红色 = 失败（点击查看错误信息）
4. 检查飞书表格中是否有数据写入

### 步骤 6: 激活定时任务

1. 测试成功后，点击右上角的 **"Active"** 开关
2. Workflow 将在每天 22:00 自动运行

---

## 🎯 工作流执行顺序

```
1. [Schedule Trigger] - 每天 22:00 触发
        ↓
2. [⚙️ 配置中心] - 设置所有配置参数（你需要修改这里！）
        ↓
3. [Bitbucket Get PRs] - 获取当天创建的 PRs
        ↓
4. [Parse PRs] - 解析 PR 数据并传递配置
        ↓
5. [Get PR Diff] - 获取代码变更（循环处理每个 PR）
        ↓
6. [Prepare AI Prompt] - 构造 AI 评估提示词
        ↓
7. [Call Qianwen API] - 调用千问进行代码评估
        ↓
8. [Categorize Issues] - 分析结果并按级别分类
        ↓
9. [Format for Feishu] - 格式化为飞书表格行
        ↓
10. [Write to Feishu Table] - 写入飞书多维表格
```

---

## 🔍 配置验证清单

在激活 workflow 之前，请确认：

- [ ] **配置节点**已修改为你的实际配置
  - [ ] BITBUCKET_WORKSPACE
  - [ ] BITBUCKET_REPO
  - [ ] FEISHU_APP_TOKEN
  - [ ] FEISHU_TABLE_ID

- [ ] **Bitbucket 凭据**已正确配置
  - [ ] Username 正确
  - [ ] App Password 有效
  - [ ] 权限包含: Repositories:Read, Pull requests:Read

- [ ] **千问 API 凭据**已正确配置
  - [ ] API Key 格式: `Bearer sk-xxxxx`
  - [ ] 账户余额充足

- [ ] **飞书凭据**已正确配置
  - [ ] Access Token 格式: `Bearer t-xxxxx`
  - [ ] Token 未过期（2小时有效期）
  - [ ] 应用权限正确

- [ ] **飞书表格**已创建
  - [ ] 所有字段名称完全一致
  - [ ] 字段类型正确
  - [ ] 已获取 App Token 和 Table ID

- [ ] **测试运行**成功
  - [ ] 所有节点显示绿色
  - [ ] 飞书表格中有数据

---

## ❌ 常见问题排查

### 问题 1: "⚙️ 配置中心" 节点找不到

**原因**: 可能导入的是企业版的 workflow 文件

**解决**: 确保导入的是 `bitbucket_pr_analysis_workflow_opensource.json` 文件

### 问题 2: Bitbucket API 返回 401 错误

**排查步骤**:
1. 检查 Bitbucket 凭据中的用户名和密码是否正确
2. 确认 App Password 权限包含 `pullrequest:read` 和 `repository:read`
3. 检查配置节点中的 WORKSPACE 和 REPO 名称是否正确
4. 测试命令:
```bash
curl -u username:app_password \
https://api.bitbucket.org/2.0/repositories/{workspace}/{repo}/pullrequests
```

### 问题 3: 千问 API 调用失败

**排查步骤**:
1. 检查 API Key 格式是否为 `Bearer sk-xxxxx`（注意有 Bearer 前缀和空格）
2. 确认阿里云账户余额充足
3. 验证 API Key 是否已启用
4. 测试命令:
```bash
curl -X POST 'https://dashscope.aliyuncs.com/api/v1/services/aigc/text-generation/generation' \
-H 'Authorization: Bearer sk-xxxxx' \
-H 'Content-Type: application/json' \
-d '{"model":"qwen-turbo","input":{"messages":[{"role":"user","content":"你好"}]}}'
```

### 问题 4: 飞书写入失败

**排查步骤**:
1. 检查 access_token 是否过期（有效期 2 小时）
2. 确认配置节点中的 FEISHU_APP_TOKEN 和 FEISHU_TABLE_ID 是否正确
3. 验证表格字段名称是否完全一致（包括空格、中文）
4. 确认应用权限包含 `bitable:record:write`
5. 测试命令:
```bash
curl -X GET \
'https://open.feishu.cn/open-apis/bitable/v1/apps/{app_token}/tables/{table_id}/records' \
-H 'Authorization: Bearer t-xxxxx'
```

### 问题 5: 没有获取到 PR

**排查步骤**:
1. 确认今天是否有新创建的 PR（状态为 OPEN）
2. 检查配置节点中的 WORKSPACE 和 REPO 名称
3. 查看 "Bitbucket Get PRs" 节点的输出
4. 尝试修改时间过滤条件（在 "Bitbucket Get PRs" 节点中）

### 问题 6: 代码 diff 太大导致超时

**解决方法**:
1. 打开 "Prepare AI Prompt" 节点
2. 找到 `maxDiffLength = 8000`
3. 调整为更小的值，例如 `5000` 或 `3000`
4. 保存后重新测试

---

## 💡 高级配置

### 修改执行时间

打开 "Schedule Trigger" 节点，修改 cron 表达式：

```
0 22 * * *   # 每天 22:00
0 9 * * *    # 每天 9:00
0 */6 * * *  # 每 6 小时
0 9 * * 1-5  # 周一到周五 9:00
```

### 过滤特定分支的 PR

打开 "Bitbucket Get PRs" 节点，修改查询参数：

```javascript
// 只获取目标分支为 main 的 PR
"q": "created_on >= {{ $now.minus({days: 1}).toFormat('yyyy-MM-dd') }} AND destination.branch.name=\"main\""

// 只获取源分支以 feature/ 开头的 PR
"q": "created_on >= {{ $now.minus({days: 1}).toFormat('yyyy-MM-dd') }} AND source.branch.name~\"feature/\""
```

### 自定义评估维度

打开 "Prepare AI Prompt" 节点，修改 prompt：

```javascript
请从以下维度进行评估：
1. **代码缺陷**：逻辑错误、边界条件
2. **注释程度**：注释充分性、清晰度
3. **合理性**：设计合理性、最佳实践
4. **安全性**：安全漏洞、敏感信息
5. **性能**：性能优化、资源使用  // 新增
6. **可维护性**：代码可读性、结构清晰度  // 新增
```

---

## 📊 预期输出效果

飞书表格中的数据示例：

| PR ID | PR 标题 | 提交者 | 问题级别 | 问题类别 | 问题描述 | 代码位置 | 改进建议 | 总体评分 |
|-------|---------|--------|----------|----------|----------|----------|----------|----------|
| 123 | 添加用户认证 | 张三 | 高 | 安全性 | 密码未加密存储 | auth.js:45 | 使用 bcrypt 加密 | 65 |
| 123 | 添加用户认证 | 张三 | 中 | 注释程度 | 关键函数缺少注释 | auth.js:30-50 | 添加函数说明 | 65 |
| 124 | 修复登录bug | 李四 | 低 | 代码缺陷 | 边界条件检查不充分 | login.js:20 | 添加空值检查 | 85 |

---

## 🎉 完成！

恭喜！你已经成功部署了 N8N 开源版的 Bitbucket PR 自动分析工作流！

**下一步**:
- 🔔 设置飞书通知（可选）
- 📈 定期查看分析结果
- 🔧 根据实际情况调整评估维度
- 📊 统计代码质量趋势

**获取帮助**:
- 查看完整文档: `WORKFLOW_CONFIG_README.md`
- 凭据配置指南: `credentials_setup_guide.json`

---

**💬 反馈建议**: 如有问题或改进建议，欢迎反馈！
