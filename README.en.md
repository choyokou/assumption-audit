[简体中文](README.md) · [English](README.en.md)

# Assumption Audit

A Codex skill for checking the assumptions behind an experiment, product decision, or technical choice. It audits its own experiment designs before delivery and reviews drafts you bring from other AI tools. When a flaw would change the outcome, the first sentence names the faulty premise and the conclusion or decision it could lead to. The skill proposes a check that exercises the suspected failure and applies the correction to the plan or text you asked for.

## Install and use

Download or clone this repository. Copy the `assumption-audit/` directory, including `SKILL.md` and `references/`, to one of these locations.

| Scope | Codex directory |
| --- | --- |
| Current project | `<project-root>/.agents/skills/assumption-audit/` |
| All projects for your user account | `~/.agents/skills/assumption-audit/` |

In the Codex CLI or IDE, type `$assumption-audit` to invoke it explicitly. You can also select it from the client's skill picker. Try this in a new session.

```text
$assumption-audit I plan to run this experiment. Give me one sentence I can paste into the experiment plan.
```

After installation, Codex can select the skill when its description matches the task. To make it the default for relevant requests, add the following instruction to the project's `AGENTS.md` or to your global `~/.codex/AGENTS.md`.

```text
When I ask you to design or review an experiment, or propose a method transfer, technical choice, or judgment that could affect a conclusion or action, use $assumption-audit before giving advice, delivering the plan, or carrying it out.
```

[OpenAI's skills guide](https://learn.chatgpt.com/docs/build-skills) · [OpenAI's AGENTS.md guide](https://learn.chatgpt.com/docs/agent-configuration/agents-md)

## Try the blind tests

The [blind-test guide](references/blind-tests.en.md) contains 21 prompts. They cover experiment design from scratch, research replication, product metrics, statistical inference, data leakage, system acceptance tests, everyday costs, strict JSON, and automatic invocation. Some prompts have sound premises and serve as controls. Give a fresh session the prompt and its source material, and keep the scoring notes separate. Check whether the opening flags the issue and its consequence, whether the proposed check exercises the critical operation, and whether the final deliverable applies the correction. The guide records isolated runs and highlights checks that can start with existing data.
