# ai-tutor-kb · AI 家庭出题方法论

把「想让孩子练什么」变成一个结构化的知识库，再由出题引擎变成一张可以打印的卷子——这个仓库开源了其中第一步的完整方法论，以及把它接进你的 AI 助手的方式。

这是一套**手动挡**工具：它不猜你的需求，也不承诺「一键搞定」。你越清楚孩子卡在哪，它产出的库和题就越准。找题、判卷、排复习这些体力活可以全部交给 AI；练什么、怎么练，由你拿主意。

## 仓库里有什么

| 目录 | 内容 |
|---|---|
| [`skill/`](skill/SKILL.md) | **建库方法论**（Claude Agent Skill 格式）：澄清四问、知识点粒度标准、「锚点 · 范围 · 定位」库名语法、样题校准、二次确认纪律 |
| [`mcp/`](mcp/README.md) | 懒人学霸 MCP 接入文档：令牌获取、各客户端配置、六个工具、批量写入与安全说明 |
| [`mcp/schema/`](mcp/schema/kb.schema.json) | 知识库交换格式（JSON Schema）——「AI 对话 → 出题执行器」之间的结构约定 |
| [`examples/`](examples/dialogue.md) | 一次完整建库对话的实录形态，以及真实生成的卷子样例 |

## 两分钟上手

**1. 装 skill**（Claude Code）：

```bash
git clone https://github.com/fouber/ai-tutor-kb.git
cp -r ai-tutor-kb/skill ~/.claude/skills/ai-tutor-kb
```

claude.ai 网页版：设置 → 功能 → Skills，上传 `skill/` 目录。

**2. 连 MCP**（可选，但强烈建议）：在[懒人学霸](https://app.lanrenxueba.com)「我的 → AI 连接器」创建令牌，然后：

```bash
claude mcp add --transport http lanrenxueba https://app.lanrenxueba.com/api/mcp \
  --header "Authorization: Bearer lrxb_你的令牌"
```

其他客户端配置见 [mcp/README.md](mcp/README.md)。

**3. 开聊**：不需要记任何命令，直接说人话——

> 「孩子这学期计算老出错，尤其是退位减法，你说怎么办？」

助手会先拉学情报告、追问到可出题的粒度、拟样题和你校准难度，把完整的库结构列给你确认，然后一键落库。你打开链接进入学习页，点「开始一次练习」，打印、批阅、复习排期都在应用内闭环。完整对话形态见 [examples/dialogue.md](examples/dialogue.md)。

## 只用方法论、不接 MCP，行不行？

行。skill 本身不依赖任何服务——你可以拿这套方法论在任何 LLM 里产出结构清晰的知识库清单，然后手动录入到你顺手的工具里。方法论是开放的；懒人学霸只是把「结构 → 卷子 → 批阅 → 复习」这段执行链路做成了闭环的那个选项。

## 边界，有话直说

- 这套东西**有门槛**：你需要说得清「想练什么」。说不清的时候，它的产出和你在任何 AI 里得到的一样平庸。
- 出题引擎的提示词工程（真题风格蒸馏等）不在本仓库开源范围。
- MCP 写入只创建私有库、不消耗任何额度；财务与账号操作在接口能力之外（详见 [mcp/README.md](mcp/README.md) 安全说明）。

## License

[MIT](LICENSE)
