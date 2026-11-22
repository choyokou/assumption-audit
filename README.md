# Assumption Audit · 前提审查

一个面向 Codex 的 Skill。在写实验方案、做产品决策或选技术路线时，检查目标、证据和做法是否对得上。发现重大问题时，第一句话说清哪个前提有问题、照做会误判什么，给出能触发该问题的小验证，再把纠正落实到最终方案。

An Agent Skill for catching consequential reasoning errors, proposing a concrete check, and carrying the correction into the requested deliverable.

## 安装与调用

下载或克隆本仓库，把包含 `SKILL.md` 和 `references/` 的 `assumption-audit/` 目录作为整体复制到目标位置。

| 使用范围 | Codex 目录 |
| --- | --- |
| 当前项目 | `<项目根目录>/.agents/skills/assumption-audit/` |
| 当前用户的所有项目 | `~/.agents/skills/assumption-audit/` |

在 Codex CLI 或 IDE 中输入 `$assumption-audit` 显式调用。也可以在客户端的 Skill 选择器中选中它。新会话里可以这样用。

```text
$assumption-audit 我打算按这个实验方案跑。请给我一句可直接放进实验计划的安排。
```

安装后，Codex 可以根据任务描述选择 Skill。希望它在符合范围的任务中默认使用时，可以把下面这句话加到项目的 `AGENTS.md`，或加到自己的全局 `~/.codex/AGENTS.md`。

```text
当我提出会影响结论或行动的实验方案、方法迁移、技术选型或判断时，在给出建议、交付内容或执行前使用 $assumption-audit。
```

[OpenAI 的 Skill 指南](https://learn.chatgpt.com/docs/build-skills) · [OpenAI 的 AGENTS.md 指南](https://learn.chatgpt.com/docs/agent-configuration/agents-md)

## 看看它是否好用

[盲测指南](references/blind-tests.md)收录 20 道题，覆盖论文复现、产品指标和统计推断、数据泄漏、系统验收、日常成本、严格 JSON、自动触发和正常对照。把题干和指定材料发给新会话，自己留着评分答案。核对首句提醒、验证是否触发关键操作，以及最终方案是否落实了纠正。指南还记录了隔离运行结果和可优先用现有数据完成的检查。
