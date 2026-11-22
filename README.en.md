[简体中文](README.md) · [English](README.en.md)

# Assumption Audit

[Install and use](#install-and-use) · [21 blind-test prompts](references/blind-tests.en.md) · [MIT license](LICENSE)

## The experiment is over. It still can’t answer your question.

Assumption Audit is a Codex skill that checks key assumptions before AI hands over an experiment it designed. It adds a quick check and spells out which step to pause if the check fails. It also reviews your plans and drafts from other AI tools.

*Both responses to the same prompts found the core risks. These examples show how their final plans differed in what to verify and when to pause.*

### The model weights fit. The components may do different jobs.

![Fictional compute bill for 576 GPU hours](assets/promo/experiment-bill.jpg)

*The 576 GPU-hour bill, illustrations, and reactions are fictional. The excerpts are translated from Chinese.*

Independently trained models can use components at the same index for different jobs. A lower math score after transfer could mean the components were misaligned. Check the alignment on held-out samples before running the main experiment.

- **Without the skill in this test** “If match quality is very low, the preregistered experiment can still run. Interpret the results as the success or failure of that matching and transplant method.”
- **With the skill in this test** “Resume the main experiment only after the revised plan passes validation on held-out inputs.”

### A library default changes the method you’re trying to replicate

![A research workstation displaying the np.cov call that changes the calculation](assets/promo/covariance-default.jpg)

The paper uses the raw data. `np.cov` subtracts the mean first. The plan with the skill checks which directions the paper’s formula and the code select from the same data, and pauses the formal comparison if they disagree.

### The cache check passed without evicting a single entry

![Tests reach only 384 tokens, while cache eviction starts above 512](assets/promo/eviction-coverage.jpg)

The test reached **384** entries. Eviction starts above **512**. The plan with the skill crosses that threshold, keeps the same entries in both implementations, and compares their outputs step by step. If the outputs disagree, pause the long-context evaluation.

> “The plots are done. Now I have to rewrite step one.”

![A researcher in a fictional scene holding his head over finished experiment plots late at night](assets/promo/researcher-reaction.jpg)

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
