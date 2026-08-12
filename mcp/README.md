# 懒人学霸 MCP 接入

远程 MCP 服务（Streamable HTTP，无状态），让你的 AI 助手直接读孩子的学情、创建/修改知识库。出题、打印、批阅、复习调度在懒人学霸应用内完成，不经此接口。

- 端点：`https://app.lanrenxueba.com/api/mcp`
- 认证：`Authorization: Bearer lrxb_...`（个人 API 令牌）

## 获取令牌

登录懒人学霸 → **我的 → AI 连接器** → 创建令牌。明文只显示一次，丢了就删除重建。令牌只能访问下面六个工具，碰不到支付与账号设置；可随时在同一页面删除，相关连接立即失效。

⚠️ 令牌只写进客户端配置，**不要粘贴到对话里**。

## 各客户端配置

**Claude Code**

```bash
claude mcp add --transport http lanrenxueba https://app.lanrenxueba.com/api/mcp \
  --header "Authorization: Bearer lrxb_你的令牌"
```

**claude.ai（网页/桌面）**：设置 → 连接器 → 添加自定义连接器，填端点 URL 与上述 Authorization 头。

**Cursor**（`~/.cursor/mcp.json`）：

```json
{
  "mcpServers": {
    "lanrenxueba": {
      "url": "https://app.lanrenxueba.com/api/mcp",
      "headers": { "Authorization": "Bearer lrxb_你的令牌" }
    }
  }
}
```

配合本仓库的 [skill](../skill/SKILL.md) 使用效果最佳：MCP 负责连接，skill 负责教会助手怎么用好。

## 工具一览

| 工具 | 作用 |
|---|---|
| `get_study_report` | 学习报告：连续打卡、各库熟练度分布、今日待复习、薄弱知识点 TOP、最近练习量、出题额度余额；传 `kbId` 聚焦单库时另给按章熟练度全景 |
| `get_wrong_book` | 读某个库的错题本（只读，最近答错在前）：题面、选项与答案、学生作答（有记录时；纸面练习可能只回传对错）、知识点熟练度与重练熟练度。用于分析错因、规划专项库；变式重练本身在应用内的错题本进行 |
| `list_kbs` | 列出我创建的与在学的知识库 |
| `get_kb` | 看某个库的结构；传 `chapterId` 读整章知识点全文 |
| `create_kb` | 创建知识库（含完整章节与知识点），落为**私有**库，返回库链接；中途失败自动清理半成品，整包重试即可 |
| `update_kb` | 修改自己创建的库（改信息 / 加章节 / 增删改知识点），按操作列表顺序执行。`add_chapter` 可内联 `points` 建章即塞点 |

知识库数据结构（`create_kb` 的入参形状）见 [schema/kb.schema.json](schema/kb.schema.json)。

## 批量写入与大库

- **限额**：每库 ≤60 章、≤600 知识点；`add_points` / `add_chapter` 内联单次 ≤200 条。
- **分片**：单次调用建议 ≤100 个知识点（工具参数是模型一次性生成的，过大可能被输出上限截断）。更大的库：`create_kb` 先提交前面的章节，剩余用 `update_kb` 的 `add_chapter` 逐章补齐。
- **重试安全（幂等）**：同章内 `term` 相同的条目自动跳过、同名章节自动复用——超时或中断后把该章整包重传即可，不会产生重复数据。
- 写入响应会回显写入后的知识点列表，无需再调 `get_kb` 核对。

## 设计与安全说明

- **写入只建私有库**：公开、分享由用户在应用内操作；助手提交的库在用户出题练习前是惰性的——不出题、不扣费、不进任何人视野，等价于草稿。
- **确认纪律在对话侧**：skill 要求助手在调用写工具前把完整清单列给用户确认；服务端 `initialize` 的 instructions 里同样声明了这条纪律。
- **令牌**：服务端只存 SHA-256 哈希；鉴权中间件仅挂在 `/api/mcp` 一条路由上，权限边界由路由拓扑保证。
- **计费**：本接口所有工具均不消耗出题额度。出题发生在应用内，按题数计费。
