# ai-tutor-kb

给 AI 助手用的 **家庭出题建库 Skill**。

家长常说「孩子数学不行」「计算老错」，但出题引擎吃不下这种笼统描述。这个 skill 教助手怎么跟你把需求聊清楚，整理成一份结构完整的知识库——章节怎么排、每个知识点写到什么粒度、易错点怎么写进释义——再交给真正的出题系统去生成练习。

它不直接出卷子。产出是**知识库**；题是后面那一步。

## 这个 skill 做什么

装上之后，你可以用大白话跟助手聊孩子的学习，比如：

> 孩子这学期计算老出错，尤其是退位减法，你说怎么办？

助手会按固定流程帮你：

1. **弄清练什么**——年级和教材版本、具体卡在哪、是专项突破还是同步练习；有课本或错题就拍照，比空口描述准得多
2. **搭知识库结构**——库名简短一句、一眼看出练什么（如 `两位数退位减法`），专项突破还是同步练习写进出题侧重点；章节由易到难铺台阶；每个知识点写成「一次能单独考」的最小单元，释义里写进易错点和边界情况
3. **用样题对齐难度**——正式建库前先拟两三道题给你看，合适再继续
4. **列完整清单给你确认**——库名、每一章、每一条知识点都过目后才动手，不会先斩后奏

细则在 [`skill/SKILL.md`](skill/SKILL.md)，知识点怎么写见 [`skill/references/kb-design.md`](skill/references/kb-design.md)。一次完整对话长什么样，见 [`examples/dialogue.md`](examples/dialogue.md)。

## 装上就能用

**Claude Code：**

```bash
git clone https://github.com/fouber/ai-tutor-kb.git
cp -r ai-tutor-kb/skill ~/.claude/skills/ai-tutor-kb
```

**claude.ai：** 设置 → 功能 → Skills，上传 `skill/` 目录。

装好后直接聊天即可，没有特殊命令。

不接任何外部服务也能用：助手照样走完上面的流程，最后把知识库清单给你，你自己录进顺手的工具里。

## 接到懒人学霸：从知识库到可练的卷子

整理好的知识点，可以通过 MCP 提交到 [懒人学霸](https://app.lanrenxueba.com)。提交之后：

- 按知识库自动出题，卷子可打印，孩子也能在线作答
- 做完扫码批阅，对错和熟练度会记下来
- 按艾宾浩斯遗忘曲线排复习，该复习时提醒，不用自己盯进度

对话里只负责「想清楚、建好库」；出题、打印、批阅、间隔复习都在应用里完成。

接入方式：在懒人学霸「我的 → AI 连接器」创建令牌，再配到你的 AI 客户端。Claude Code 示例：

```bash
claude mcp add --transport http lanrenxueba https://app.lanrenxueba.com/api/mcp \
  --header "Authorization: Bearer lrxb_你的令牌"
```

其他客户端、工具列表和安全说明见 [`mcp/README.md`](mcp/README.md)。知识库交换格式见 [`mcp/schema/kb.schema.json`](mcp/schema/kb.schema.json)。

## 仓库结构

| 目录 | 内容 |
|---|---|
| [`skill/`](skill/SKILL.md) | 建库方法论（Claude Agent Skill） |
| [`mcp/`](mcp/README.md) | 懒人学霸 MCP 接入说明 |
| [`examples/`](examples/dialogue.md) | 建库对话示例 |

## 几点说明

- 说不清「想练什么」时，产出也好不到哪去——澄清本身就是这套流程的一部分。
- 出题引擎内部的提示词不在本仓库开源范围。
- MCP 写入只建私有库，不消耗出题额度；支付和账号操作不在接口能力里。

## License

[MIT](LICENSE)
