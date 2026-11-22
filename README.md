[简体中文](README.md) · [English](README.en.md)

# Assumption Audit · 前提审查

[安装与调用](#安装与调用) · [21 道盲测题](references/blind-tests.md) · [MIT 许可证](LICENSE)

## 576 个 GPU 小时之后，你才发现实验从一开始就设计错了

![虚构算力账单，576 GPU 小时](assets/promo/experiment-bill.jpg)

*算力账单、配图和人物反应为虚构演示。下方引文来自一次同题对照。*

你让 AI 设计实验，把一个模型的数学能力搬到另一个模型。两个模型结构一样，权重也装得进去。可它们是分开训练的，按编号对应的部件可能干着完全不同的活。跑完掉分，你仍然不知道数学能力能不能搬过去。

- **默认方案**。「若匹配质量很低，仍可运行预注册实验，但需将结果解释为该匹配与移植方法的成败。」
- **加载 Assumption Audit**。「只有新方案重新通过留出验证才能恢复主实验。」

### 「我用的明明是标准函数，怎么跑成了另一种方法？」

![工作台显示 np.cov 调用，说明库函数默认行为可能改变实验](assets/promo/covariance-default.jpg)

论文的计算直接用原始数据。代码里的 `np.cov` 会先减去均值。**数据一样，最后挑出的方向可能不同。** 这次对照里，两边都改回了论文公式。加载 Skill 后，最终方案还要求先用**同一份数据**核对两种写法选出的方向，没对上就暂停正式比较。

### 「验收明明通过了。后来一看，真正要验的那步一次都没跑。」

![测试最长覆盖 384 个 token，缓存超过 512 个才开始淘汰](assets/promo/eviction-coverage.jpg)

缓存超过 **512** 个条目才会删除旧内容，所有试跑却最多只到了 **384**。两边都看出了这次验收没触发删除。加载 Skill 的方案还写清了**真正删掉之后怎么验**。让参考版和实际删除版保留同样的条目，再逐步比较输出。对不上，就暂停长文本评测。

*同题对照使用 `gpt-6.1-sol`，四道题分别在未安装和安装 Skill 的独立会话里各跑一次，隔离了个人全局 `AGENTS.md`。两种配置都发现了四道题的核心风险。这里展示的是其中三道题的最终方案差异。*

**Assumption Audit** 是一个 MIT 授权的 Codex Skill。它在 AI 交付自己设计的实验、审查已有方案或作出重要判断前，指出可能改变结论的前提，提出一个能真正触发疑似问题的小检查，并把纠正和停止条件写进最终方案。

> 「图都画完了。现在得回去重写第一步。」

![虚构场景里的研究者深夜看着已经画完的实验图表扶额](assets/promo/researcher-reaction.jpg)

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
当我请你设计或审查实验，或提出会影响结论或行动的方法迁移、技术选型或判断时，在给出建议、交付内容或执行前使用 $assumption-audit。
```

[OpenAI 的 Skill 指南](https://learn.chatgpt.com/docs/build-skills) · [OpenAI 的 AGENTS.md 指南](https://learn.chatgpt.com/docs/agent-configuration/agents-md)

## 看看它是否好用

[盲测指南](references/blind-tests.md)收录 21 道题，覆盖从零设计实验、论文复现、产品指标和统计推断、数据泄漏、系统验收、日常成本、严格 JSON、自动触发和正常对照。把题干和指定材料发给新会话，自己留着评分答案。核对首句提醒、验证是否触发关键操作，以及最终方案是否落实了纠正。指南还记录了隔离运行结果和可优先用现有数据完成的检查。
